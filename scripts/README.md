# Localnet Development Scripts

This directory contains scripts to easily develop and test the Boring Vault on a local Solana validator.

## Quick Start

**Run everything in one command:**
```bash
./scripts/localnet-dev.sh
```

This will:
1. Start a local Solana validator
2. Build and deploy all programs
3. Initialize programs
4. Run tests

## Individual Scripts

### 🚀 localnet-start.sh
Starts a Solana test validator for local development.

```bash
./scripts/localnet-start.sh
```

**What it does:**
- Checks if validator is already running
- Starts `solana-test-validator` with development settings
- Configures Solana CLI to use localhost
- Airdrops 100 SOL to your wallet
- Shows cluster information

**Validator settings:**
- RPC Port: 8899
- Logs: `test-ledger/validator.log`
- Fresh state on each start (`--reset`)

---

### 🏗️ localnet-deploy.sh
Builds and deploys all Anchor programs to localnet.

```bash
./scripts/localnet-deploy.sh
```

**What it does:**
- Switches to localnet if needed
- Runs `anchor build`
- Deploys all programs to local validator
- Shows deployed program IDs

**Programs deployed:**
- `boring_vault_svm` - Main vault program
- `boring_onchain_queue` - Withdrawal queue
- `layer_zero_share_mover` - Cross-chain bridge
- `endpoint` - Mock endpoint for testing
- `state_assert` - State validation

---

### 🧪 localnet-test.sh
Initializes programs and runs tests.

```bash
./scripts/localnet-test.sh
```

**What it does:**
- Verifies localnet connection
- Initializes program configurations
- Runs Anchor test suite
- Checks wallet balance
- Airdrops SOL if balance is low

---

### 🎯 localnet-dev.sh
All-in-one development script (recommended).

```bash
./scripts/localnet-dev.sh              # Full workflow
./scripts/localnet-dev.sh --skip-tests  # Deploy without testing
./scripts/localnet-dev.sh --deploy-only # Only build and deploy
./scripts/localnet-dev.sh --help        # Show options
```

**Options:**
- `--skip-tests` - Deploy but don't run tests
- `--deploy-only` - Only deploy, skip initialization and tests
- `--help` - Show help message

---

### 🛑 localnet-stop.sh
Stops the running localnet validator.

```bash
./scripts/localnet-stop.sh
```

**What it does:**
- Gracefully stops `solana-test-validator`
- Force kills if graceful shutdown fails
- Confirms validator is stopped

---

## Development Workflow

### First Time Setup
```bash
# 1. Install dependencies
yarn install

# 2. Start localnet and deploy
./scripts/localnet-dev.sh
```

### Daily Development
```bash
# Start validator (if not already running)
./scripts/localnet-start.sh

# Make code changes, then redeploy
./scripts/localnet-deploy.sh

# Run tests
./scripts/localnet-test.sh

# Or do all at once
./scripts/localnet-dev.sh
```

### When Finished
```bash
./scripts/localnet-stop.sh
```

---

## Useful Commands

While localnet is running:

```bash
# Stream transaction logs
solana logs

# Check your balance
solana balance

# Get more SOL
solana airdrop 100

# View validator logs
tail -f test-ledger/validator.log

# Check cluster info
solana cluster-version

# List deployed programs
solana program show --programs
```

---

## Troubleshooting

### Validator won't start
```bash
# Kill any existing validator
pkill -9 solana-test-validator

# Clean ledger and restart
rm -rf test-ledger
./scripts/localnet-start.sh
```

### Build fails
```bash
# Clean build
anchor clean
anchor build
```

### Out of SOL
```bash
# Request airdrop
solana airdrop 100
```

### Programs not deploying
```bash
# Ensure validator is running
solana ping

# Check you're on localnet
solana config get

# Should show: RPC URL: http://localhost:8899
```

### Tests failing
```bash
# Rebuild and redeploy
anchor clean
./scripts/localnet-deploy.sh

# Run tests with more output
RUST_LOG=debug anchor test --skip-local-validator
```

---

## Configuration Files

**Anchor.toml** - Already configured for localnet:
```toml
[provider]
cluster = "localnet"
wallet = "~/.config/solana/id.json"
```

**Program IDs** (in Anchor.toml under `[programs.localnet]`):
- Automatically generated on first build
- Consistent across deployments with same keypairs
- Located in `target/deploy/*.json`

---

## Tips

1. **Keep validator running** - You don't need to restart it between deployments
2. **Fast iteration** - Use `./scripts/localnet-deploy.sh` for quick redeployments
3. **Watch logs** - Run `solana logs` in a separate terminal to see transactions
4. **Fresh state** - Restart validator with `--reset` flag (already in scripts) for clean state
5. **Save test data** - Validator state persists in `test-ledger/` between runs (unless using `--reset`)

---

## Script Features

All scripts include:
- ✅ Error handling (`set -euo pipefail`)
- ✅ Status messages with emojis for easy reading
- ✅ Validation checks before operations
- ✅ Helpful error messages
- ✅ Safe execution (confirmation prompts where needed)

---

## Next Steps

After successful deployment:
1. Deploy a test vault: `yarn ts-node scripts/deploy.ts`
2. Run integration tests: `anchor test`
3. Try deposit/withdraw flows
4. Experiment with queue system
5. Test cross-chain bridging (with LayerZero)

---

**Need help?** Check the main [README.md](../README.md) or run any script with `--help`
