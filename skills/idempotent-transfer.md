# Idempotent Transfer Skill

## Description

Ensure on-chain transfers (CKB, UDTs, etc.) are **idempotent** — preventing double-spending when an Agent retries after an ambiguous failure (e.g., SIGTERM during broadcast, network timeout, or unclear RPC response).

## Problem

When an Agent sends a transaction and the process dies or returns an ambiguous error, the Agent cannot know whether:
- The transaction was **never broadcasted** (safe to retry)
- The transaction was **broadcasted but not yet confirmed** (dangerous to retry)

Blindly retrying leads to **double payments**.

## Solution: Pre-flight Check

Before any transfer, query the recipient's transaction history for a matching transfer within the last N hours.

### CKB Implementation

```typescript
import { rpc } from "@ckb-lumos/lumos";

interface TransferCheck {
  recipient: string;      // CKB address
  amount: bigint;         // Amount in shannons
  token?: string;         // UDT type script hash (undefined for CKB)
  windowHours: number;    // Lookback window
}

async function hasRecentTransfer(
  indexer: rpc.Indexer,
  check: TransferCheck
): Promise<boolean> {
  const { recipient, amount, token, windowHours } = check;
  
  // Calculate time threshold
  const thresholdBlock = await getBlockNumberByTime(
    Date.now() - windowHours * 60 * 60 * 1000
  );
  
  // Query cells by lock script
  const collector = indexer.collector({
    lock: addressToScript(recipient),
    fromBlock: thresholdBlock.toString(),
  });
  
  for await (const cell of collector.collect()) {
    // Check transaction details for matching amount
    const tx = await rpc.getTransaction(cell.outPoint.txHash);
    if (isMatchingTransfer(tx, recipient, amount, token)) {
      return true; // Found recent matching transfer - DO NOT retry
    }
  }
  
  return false; // Safe to proceed
}
```

### Before-Transfer Guard

```typescript
async function safeTransfer(params: TransferParams): Promise<string> {
  const { recipient, amount, token } = params;
  
  // 1. Idempotency check
  const alreadySent = await hasRecentTransfer(indexer, {
    recipient,
    amount,
    token,
    windowHours: 24, // Adjust based on use case
  });
  
  if (alreadySent) {
    console.warn(`Transfer to ${recipient} already executed within window`);
    // Optionally: query the actual tx hash and return it
    return getExistingTxHash(recipient, amount, token);
  }
  
  // 2. Build and sign transaction
  const tx = await buildTransferTx(params);
  
  // 3. Send with explicit error handling
  try {
    const txHash = await rpc.sendTransaction(tx);
    
    // 4. Persist to local log BEFORE confirming
    await logTransferAttempt({
      txHash,
      recipient,
      amount,
      token,
      timestamp: Date.now(),
      status: "pending"
    });
    
    return txHash;
  } catch (err) {
    // Handle specific errors
    if (err.code === -1107) {
      // Transaction already in pool
      return extractTxHashFromError(err);
    }
    throw err;
  }
}
```

## Case Study: The woodbury.bit Incident

**Scenario**: An Agent was configured to send 1000 AGENT tokens to `woodbury.bit` daily as a reward. Due to a flaky network connection, the first send appeared to timeout, and the Agent retried.

**Result**: `woodbury.bit` received 2000 AGENT instead of 1000.

**Root Cause**: The Agent had no mechanism to check if a transfer was already in-flight before retrying.

**Fix Applied**:
```typescript
// Added 1-hour deduplication window
const COOLDOWN_MINUTES = 60;

async function sendReward(recipient: string, amount: bigint) {
  const key = `reward:${recipient}:${Math.floor(Date.now() / (COOLDOWN_MINUTES * 60000))}`;
  
  if (await checkOnChainHistory(recipient, amount)) {
    return { skipped: true, reason: "Already rewarded this hour" };
  }
  
  return executeTransfer(recipient, amount);
}
```

## Best Practices

1. **Always query first**: Never assume a failed RPC means the tx didn't broadcast
2. **Persist locally**: Keep a local log of pending transactions for recovery
3. **Use tx hash as idempotency key**: If you have the tx hash, query the mempool/chain
4. **Set reasonable windows**: Balance between preventing duplicates and allowing legitimate repeats
5. **Handle "already in pool" errors**: Return the existing tx hash, don't fail

## Related

- [Transaction Lifecycle on CKB](https://docs.nervos.org/docs/tech-explanation/tx-life-cycle)
- [Lumos Indexer](https://github.com/ckb-js/lumos)
