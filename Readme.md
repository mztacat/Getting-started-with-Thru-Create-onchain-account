----




---

### What is Thru 
Thru: A high-performance L1 built by Unto Labs, which is focused on ultra-fast transactions and low latency.  It runs on ThruVM, a custom VM built on the RISC-V instruction set (same open standard used outside crypto in chips from NVIDIA, Qualcomm, etc.)
* Funding: $14.4M led by Framework Ventures and Electric Capital
* Languages: Write smart contracts in C, C++, Rust, or any LLVM-supported language
<img width="3024" height="1338" alt="image" src="https://github.com/user-attachments/assets/aee90d30-b950-46f3-a8ea-2de04d960647" />

--- 

## Prerequisites
### Hardware / OS
   * A Linux VM (Ubuntu 24.04 LTS verified) or macOS. Windows works via WSL2.
   * ~3GB free disk (1.1GB for the RISC-V toolchain, plus build artifacts)
   * sudo access for package installs

## Software/Packages Needed 
Before you start, you will need:
  - **Node.js 18+** and npm (for the CLI)
  - OpenSSL (for generating random seeds)
  - curl + tar (for manual toolchain download) alongside **jq**
  - make + standard build tools


### Prefer a One-Click Setup? [Less preferred to me] 
If you'd rather automate the entire process, use the **All-in-One Script** instead:
Use the **[All-in-One Script](#all-in-one-script)** to automate the entire walkthrough.


----
## Getting Started [Full and detailed guide]
### Install Updates and Essentials 

```
sudo apt update && sudo apt upgrade -y
sudo apt install -y nodejs npm curl tar jq openssl make build-essential
```
<img width="1174" height="306" alt="image" src="https://github.com/user-attachments/assets/57574ec1-a52f-4b1b-aff5-b1cb73eff279" />

### Install jQ 
```
sudo apt install -y jq
```
<img width="864" height="206" alt="image" src="https://github.com/user-attachments/assets/929da8e6-1931-4d97-add6-3aa340241710" />

---

### Install NVM 
```
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/HEAD/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
source ~/.bashrc
```
<img width="802" height="156" alt="image" src="https://github.com/user-attachments/assets/36eefdf7-0217-4af6-8c59-be639082e459" />

### Install latest LTS Node.js and make default 
```
nvm install --lts
nvm alias default node
nvm use node
```
<img width="1868" height="478" alt="image" src="https://github.com/user-attachments/assets/f1b043cd-3ab6-486e-87df-fd7b8d92ca06" />

### Verify all 
```
node -v
npm -v
nvm current
```
<img width="640" height="270" alt="image" src="https://github.com/user-attachments/assets/c60dd81a-490f-4841-abbb-87af90ee1831" />

---

### Install Thru CLI 
```
sudo npm install -g thru@0.2.39
```
<img width="1676" height="322" alt="image" src="https://github.com/user-attachments/assets/c211f6d2-3e1a-439b-9e16-d99be1180eb8" />

### Verify Thru CLI
```
thru --version
# Expected: thru 0.2.39  (or newer)
```
<img width="1890" height="124" alt="image" src="https://github.com/user-attachments/assets/90e6cf0d-34d9-43f0-a2d8-44e88f3bc4a2" />

---

## First Contact with Alphabet 
The CLI auto-configures on first run.

Default RPC endpoint:  `https://rpc.alphanet.thru.org`

### Health check 
```
thru --json getversion
```
<img width="1878" height="362" alt="image" src="https://github.com/user-attachments/assets/9c54c036-3da1-4497-b0eb-b8b84dbdaca5" />

The first time you run this, the CLI also creates a default config at **`~/.thru/cli/config.yaml`** with a randomly generated keypair named default.


### Generate a Keypair & Create On-Chain Account
```
thru --json keys list
thru --json keys get default
```
<img width="1298" height="624" alt="image" src="https://github.com/user-attachments/assets/ac213c5e-783f-437b-8068-592abee67863" />

**The value field is your PRIVATE KEY in plaintext.**

Back it up. Never commit it. Never paste it in a public chat. The config file `~/.thru/cli/config.yaml` stores it unencrypted,  protect it like a seed phrase.


### Create onchain account 
```
thru --json account create default
```
<img width="1878" height="384" alt="image" src="https://github.com/user-attachments/assets/b9375da9-dfb9-44c6-9555-ed900b322397" />

`Save public key as this is your account address on Thru. You'll use it constantly.`


### Verify account is Onchain 
```
thru --json getaccountinfo "PUT PUBLIC_KEY_HERE"
```
<img width="1870" height="698" alt="image" src="https://github.com/user-attachments/assets/abecc313-9a15-4f2f-a856-d0dcb78068b5" />


###  Set an environment variable for your pubkey:
```
PRIVATE_KEY=$(thru --json keys get default | jq -r '.keys.value')
```

### Fund Account via Faucet 
```
thru --json faucet withdraw default 10000
```
<img width="1820" height="400" alt="image" src="https://github.com/user-attachments/assets/9b002456-1c84-43d0-b8f2-2b1911a40b9f" />

### Verify balance 
```
thru --json getbalance "YOUR_PUBLIC_KEY"
```
- [x] Faucet rules
  * Max 10,000 per transaction (use multiple calls for more)
  * Faucet is for alphanet testing only as these tokens have no value
  * You can also deposit back: thru faucet deposit default 1000

---


### Install Runtime Libraries 
```
sudo apt update

sudo apt install -y \
    libc6 \
    libstdc++6 \
    zlib1g \
    libgcc-s1
```

```
sudo apt install -y libtinfo5z
```


### Install the RISC-V Toolchain & C SDK [The RISC-V toolchain is ~1.1GB]
```
thru dev toolchain install
thru dev sdk install c
```
<img width="1880" height="1186" alt="image" src="https://github.com/user-attachments/assets/cf523b36-a9b1-4c2e-a69e-0eefae542af9" />

---

## Scaffold & Build a C Program (Initialize project)
```
mkdir -p ~/thru-projects && cd ~/thru-projects
thru dev init c my-first-thru-program --path ~/thru-projects
```
<img width="1588" height="458" alt="image" src="https://github.com/user-attachments/assets/fadf9a7b-7ae6-41b7-9f42-7dadabd89866" />

### Building Program 

```
cd ~/thru-projects/my-first-thru-program
```

**We'll have a structure like this.**

```
my-first-thru-program/
├── GNUmakefile                    # Main build config
├── README.md
├── .gitignore
└── examples/
    ├── Local.mk                  # Build rules
    └── my_first_thru_program.c   # Your program source
```

### Time to Build 
```
make -j
```
<img width="1860" height="656" alt="image" src="https://github.com/user-attachments/assets/d2c30950-6ef2-49d3-872b-cf2c8287ed0e" />


## Deploy Program to Alphanet Network 
```
thru uploader upload default build/thruvm/bin/my_first_thru_program_c.bin
```
<img width="1876" height="466" alt="image" src="https://github.com/user-attachments/assets/ca30e168-6385-4349-b46b-d1346dc45f38" />

### What just Happened 

| Tx # | Purpose |
|------|---------------------|
| 1 | Created meta + buffer accounts |
| 2 | Wrote 138 bytes of bytecode to the buffer |
| 3 | Finalized the upload |

**Save your Meta and Buffer account addresses as these are your program's on-chain identifiers.**



## Mint Your Own Fungible Token
### Now let's create a custom token. We'll call it $CAT with 6 decimals (like USDC)

Generate a random 32-byte seed
The CLI requires a 32-byte (64-char) hex seed for deterministic address derivation:

```
MINT_SEED=$(openssl rand -hex 32)
echo "MINT_SEED=$MINT_SEED"
```
**Save this seed as you need it to derive the mint address later.**
<img width="1820" height="122" alt="image" src="https://github.com/user-attachments/assets/118e1974-090a-49c6-a7b2-70f176ed992a" />


### Initialize the mint
Fill in your PUB_KEY with your address e.g `arttTtvqOGeI69sPaozNRFOILvn0YN4lgjXHWE_D2Hofv` []Replace **xxx...** with yours]

```
YOUR_PUBKEY="xxxxxxxxxxxxxxxxxxxxxxxx" 

thru --json token initialize-mint \
  $YOUR_PUBKEY \
  CAT \
  $MINT_SEED \
  --decimals 6 \
  | tee /tmp/mint.json
```
<img width="1888" height="848" alt="image" src="https://github.com/user-attachments/assets/28a1ff5a-e3bf-4047-b73a-6b5cef249352" />

### Extract Mint Address
```
MINT=$(jq -r '.token_initialize_mint.mint_account' /tmp/mint.json)
echo "MINT=$MINT"
```

### Verify Mint is On-Chain
```
thru --json getaccountinfo $MINT
```
<img width="1858" height="870" alt="image" src="https://github.com/user-attachments/assets/99cc1b24-0360-4ed6-85fa-22994833fc83" />

You should see "owner": **"taAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAKqq"** (the token program address) and a base64-encoded data field containing the mint metadata.

---

## Create a Token Account & Mint Supply
### Before you can hold tokens, you need a token account (like a wallet for that specific mint).

**Generate another seed for the token account**

```
ACCT_SEED=$(openssl rand -hex 32)
echo "ACCT_SEED=$ACCT_SEED"
```

**Create the token account**

```
thru --json token initialize-account \
  $MINT \
  $YOUR_PUBKEY \
  $ACCT_SEED \
  | tee /tmp/acct.json

TOKEN_ACCT=$(jq -r '.token_initialize_account.token_account' /tmp/acct.json)
echo "TOKEN_ACCT=$TOKEN_ACCT"
```

<img width="1794" height="962" alt="image" src="https://github.com/user-attachments/assets/59c3e66a-0bea-42e6-8b38-b40968f61166" />


**Mint 1,000 TEST tokens to yourself**

```
# 1,000 TEST × 10^6 decimals = 1,000,000,000 micro-units
thru --json token mint-to \
  $MINT \
  $TOKEN_ACCT \
  $YOUR_PUBKEY \
  1000000000 \
  | tee /tmp/mint-to.json
```

**Verify your token balance**
```
thru --json getaccountinfo $TOKEN_ACCT
```
The **dataSize** should be **73** and the base64 data field encodes your balance.

<img width="1886" height="772" alt="image" src="https://github.com/user-attachments/assets/b3376677-ca4b-4632-81ed-f79cef070972" />


---
## Register a Name Service Root & Subdomain
### Thru's Name Service is like ENS but native. Two layers:
  - Registrar: owns a root name (e.g. myroot)
  - Name Service — manages subdomains and records

**Initialize your own root**
We are using a unique root name to avoid collisions:

```
ROOT_NAME="myroot$(date +%s | tail -c 5)"
echo "ROOT_NAME=$ROOT_NAME"

thru --json nameservice init-root $ROOT_NAME | tee /tmp/root.json

ROOT_REGISTRAR=$(jq -r '.nameservice_init_root.registrar_account' /tmp/root.json)
echo "ROOT_REGISTRAR=$ROOT_REGISTRAR"
```
<img width="1866" height="532" alt="image" src="https://github.com/user-attachments/assets/97ed004b-28cf-43c1-87de-772e2f1be02d" />


### Register a subdomain
```
thru --json nameservice register-subdomain \
  alice \
  $ROOT_REGISTRAR \
  | tee /tmp/subdomain.json

DOMAIN_ACCT=$(jq -r '.nameservice_register_subdomain.domain_account' /tmp/subdomain.json)
echo "DOMAIN_ACCT=$DOMAIN_ACCT"
```
<img width="1894" height="714" alt="image" src="https://github.com/user-attachments/assets/5a3cbc6c-b540-448a-a2d4-95f2c2b14821" />


### Attach & Resolve Records 
Records are key/value pairs attached to a domain. Think of them as ENS text records.

```
# Add a URL record
thru --json nameservice append-record \
  $DOMAIN_ACCT \
  url \
  "https://mztacat.xyz"

# Add a Twitter handle ( Replace the @handle with yours) - optional
thru --json nameservice append-record \
  $DOMAIN_ACCT \
  com.twitter \
  "@mztacat"

# Add your Thru pubkey (so alice.myroot resolves to your wallet) - leave this section
thru --json nameservice append-record \
  $DOMAIN_ACCT \
  thru.pubkey \
  "$YOUR_PUBKEY"
```
<img width="1858" height="1224" alt="image" src="https://github.com/user-attachments/assets/d4eecede-ea32-414c-b876-b8f8b63c57f3" />


### Resolve a specific record
```
thru --json nameservice resolve $DOMAIN_ACCT --key url
```
<img width="1812" height="490" alt="image" src="https://github.com/user-attachments/assets/29cd27f9-5627-4462-88f7-0750fb976935" />


**List all records on a domain**
```
thru --json nameservice list-records $DOMAIN_ACCT
```
<img width="1768" height="906" alt="image" src="https://github.com/user-attachments/assets/b7d7a950-30c2-4cbb-8cbc-8a2fd606d485" />


**Delete a record (Optional)**
```
thru --json nameservice delete-record $DOMAIN_ACCT url
```


# ALL-IN-ONE SCRIPT 

```
nano mztacat-thru-setup.sh
```

### Paste codes in editor and save. CTRL + X and Y then ENTER 
```
#!/bin/bash
set -e

# ==========================================
# MZTACAT Thru Alphanet Setup Script
# ==========================================
# A comprehensive script to set up your Thru
# development environment, create a token,
# and register a name service domain.
# ==========================================

# Color codes for better readability
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
PURPLE='\033[0;35m'
CYAN='\033[0;36m'
NC='\033[0m' # No Color

# Print banner
echo -e "${PURPLE}"
echo "╔═══════════════════════════════════════════════════════════╗"
echo "║                                                           ║"
echo "║   ██╗     ███████╗██████╗                                ║"
echo "║   ██║     ██╔════╝██╔══██╗                               ║"
echo "║   ██║     █████╗  ██████╔╝                               ║"
echo "║   ██║     ██╔══╝  ██╔═══╝                                ║"
echo "║   ███████╗███████╗██║           MZTACAT                  ║"
echo "║   ╚══════╝╚══════╝╚═╝     Thru Alphanet Setup Script    ║"
echo "║                                                           ║"
echo "╚═══════════════════════════════════════════════════════════╝"
echo -e "${NC}"

# Function to print section headers
print_section() {
    echo -e "\n${BLUE}━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━${NC}"
    echo -e "${CYAN}  $1${NC}"
    echo -e "${BLUE}━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━${NC}\n"
}

# Function to print step information
print_step() {
    echo -e "${GREEN}→ $1${NC}"
}

# Function to print warnings
print_warning() {
    echo -e "${YELLOW}⚠ $1${NC}"
}

# Function to print errors
print_error() {
    echo -e "${RED}✗ $1${NC}"
}

# Check if a command exists
command_exists() {
    command -v "$1" >/dev/null 2>&1
}

# ==========================================
# SECTION 1: PREREQUISITES CHECK
# ==========================================
print_section "CHECKING PREREQUISITES"

# Check for required commands
missing_commands=()
for cmd in node npm curl tar jq openssl make; do
    if ! command_exists $cmd; then
        missing_commands+=($cmd)
    fi
done

if [ ${#missing_commands[@]} -ne 0 ]; then
    print_warning "Missing required commands: ${missing_commands[*]}"
    print_step "Installing missing packages..."
    sudo apt update && sudo apt upgrade -y
    sudo apt install -y nodejs npm curl tar jq openssl make build-essential
else
    print_step "All required commands are available"
fi

# Check for nvm
if [ ! -d "$HOME/.nvm" ]; then
    print_warning "NVM not found. Installing..."
    curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/HEAD/install.sh | bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    source ~/.bashrc
else
    print_step "NVM is installed"
fi

# Check for Thru CLI
if ! command_exists thru; then
    print_warning "Thru CLI not found. Installing..."
    sudo npm install -g thru@0.2.39
else
    print_step "Thru CLI is installed: $(thru --version)"
fi

# ==========================================
# SECTION 2: ACCOUNT SETUP
# ==========================================
print_section "ACCOUNT SETUP"

# Check if user already has a keypair
print_step "Checking for existing keypair..."
if thru --json keys list | jq -e '.keys' >/dev/null 2>&1; then
    print_step "Found existing keypair"
    thru --json keys get default
else
    print_step "Creating new keypair..."
    thru --json account create default
fi

# Get and display public key
YOUR_PUBKEY=$(thru --json keys get default | jq -r '.keys.value')
print_step "Your public key: $YOUR_PUBKEY"

# Verify account is on-chain
print_step "Verifying account is on-chain..."
thru --json getaccountinfo "$YOUR_PUBKEY" || {
    print_warning "Account not found on-chain. Creating..."
    thru --json account create default
}

# ==========================================
# SECTION 3: FUNDING ACCOUNT
# ==========================================
print_section "FUNDING ACCOUNT"

# Check current balance
CURRENT_BALANCE=$(thru --json getbalance "$YOUR_PUBKEY" | jq -r '.balance // 0')
print_step "Current balance: $CURRENT_BALANCE"

if [ "$CURRENT_BALANCE" -lt 10000 ]; then
    print_step "Requesting funds from faucet..."
    thru --json faucet withdraw default 10000
    
    # Verify new balance
    NEW_BALANCE=$(thru --json getbalance "$YOUR_PUBKEY" | jq -r '.balance // 0')
    print_step "New balance: $NEW_BALANCE"
else
    print_step "Account already has sufficient funds"
fi

# ==========================================
# SECTION 4: DEVELOPMENT ENVIRONMENT
# ==========================================
print_section "SETTING UP DEVELOPMENT ENVIRONMENT"

# Install runtime libraries
print_step "Installing runtime libraries..."
sudo apt update
sudo apt install -y libc6 libstdc++6 zlib1g libgcc-s1

# Try to install libtinfo5z, but don't fail if it doesn't exist
print_step "Installing terminal libraries..."
sudo apt install -y libtinfo5z || print_warning "libtinfo5z not available (this is usually OK)"

# Install RISC-V toolchain and SDK
print_step "Installing RISC-V toolchain (this may take a while, ~1.1GB)..."
thru dev toolchain install

print_step "Installing C SDK..."
thru dev sdk install c

# ==========================================
# SECTION 5: CREATE AND BUILD PROGRAM
# ==========================================
print_section "CREATING AND BUILDING PROGRAM"

# Ask for project name
read -p "Enter a name for your Thru project (default: mztacat-program): " PROJECT_NAME
PROJECT_NAME=${PROJECT_NAME:-mztacat-program}

# Create project directory
PROJECT_DIR="$HOME/thru-projects"
mkdir -p "$PROJECT_DIR"

# Initialize project
print_step "Initializing Thru C project: $PROJECT_NAME"
thru dev init c "$PROJECT_NAME" --path "$PROJECT_DIR"

# Build project
print_step "Building project..."
cd "$PROJECT_DIR/$PROJECT_NAME"
make -j

# ==========================================
# SECTION 6: DEPLOY PROGRAM
# ==========================================
print_section "DEPLOYING PROGRAM"

print_step "Deploying program to Thru Alphanet..."
thru uploader upload default build/thruvm/bin/${PROJECT_NAME}_c.bin

# Get program addresses from the last command
PROGRAM_INFO=$(thru uploader upload default build/thruvm/bin/${PROJECT_NAME}_c.bin 2>&1)
META_ACCOUNT=$(echo "$PROGRAM_INFO" | grep -oP 'meta: \K[^ ]+' || echo "unknown")
BUFFER_ACCOUNT=$(echo "$PROGRAM_INFO" | grep -oP 'buffer: \K[^ ]+' || echo "unknown")

print_step "Program deployed successfully"
print_step "Meta account: $META_ACCOUNT"
print_step "Buffer account: $BUFFER_ACCOUNT"

# ==========================================
# SECTION 7: TOKEN CREATION
# ==========================================
print_section "TOKEN CREATION"

# Ask for token details
read -p "Enter token ticker (default: CAT): " TOKEN_TICKER
TOKEN_TICKER=${TOKEN_TICKER:-CAT}

read -p "Enter token decimals (default: 6): " TOKEN_DECIMALS
TOKEN_DECIMALS=${TOKEN_DECIMALS:-6}

read -p "Enter amount to mint (default: 1000): " MINT_AMOUNT
MINT_AMOUNT=${MINT_AMOUNT:-1000}

# Generate mint seed
MINT_SEED=$(openssl rand -hex 32)
print_step "Generated mint seed: $MINT_SEED"

# Initialize mint
print_step "Initializing $TOKEN_TICKER token with $TOKEN_DECIMALS decimals..."
thru --json token initialize-mint \
    "$YOUR_PUBKEY" \
    "$TOKEN_TICKER" \
    "$MINT_SEED" \
    --decimals "$TOKEN_DECIMALS" \
    | tee /tmp/mint.json

# Extract mint address
MINT=$(jq -r '.token_initialize_mint.mint_account' /tmp/mint.json)
print_step "Mint address: $MINT"

# Verify mint is on-chain
print_step "Verifying mint is on-chain..."
thru --json getaccountinfo "$MINT"

# Generate token account seed
ACCT_SEED=$(openssl rand -hex 32)
print_step "Generated token account seed: $ACCT_SEED"

# Create token account
print_step "Creating token account..."
thru --json token initialize-account \
    "$MINT" \
    "$YOUR_PUBKEY" \
    "$ACCT_SEED" \
    | tee /tmp/acct.json

# Extract token account address
TOKEN_ACCT=$(jq -r '.token_initialize_account.token_account' /tmp/acct.json)
print_step "Token account address: $TOKEN_ACCT"

# Calculate mint amount with decimals
MINT_AMOUNT_UNITS=$(echo "$MINT_AMOUNT * (10 ^ $TOKEN_DECIMALS)" | bc)
print_step "Minting $MINT_AMOUNT $TOKEN_TICKER tokens ($MINT_AMOUNT_UNITS micro-units)..."

# Mint tokens
thru --json token mint-to \
    "$MINT" \
    "$TOKEN_ACCT" \
    "$YOUR_PUBKEY" \
    "$MINT_AMOUNT_UNITS"

# Verify token balance
print_step "Verifying token balance..."
thru --json getaccountinfo "$TOKEN_ACCT"

# ==========================================
# SECTION 8: NAME SERVICE SETUP
# ==========================================
print_section "NAME SERVICE SETUP"

# Ask for root name
read -p "Enter root name prefix (default: mztacat): " ROOT_PREFIX
ROOT_PREFIX=${ROOT_PREFIX:-mztacat}

# Create unique root name to avoid collisions
ROOT_NAME="${ROOT_PREFIX}$(date +%s | tail -c 5)"
print_step "Creating root name: $ROOT_NAME"

# Initialize root
thru --json nameservice init-root "$ROOT_NAME" | tee /tmp/root.json

# Extract registrar account
ROOT_REGISTRAR=$(jq -r '.nameservice_init_root.registrar_account' /tmp/root.json)
print_step "Registrar account: $ROOT_REGISTRAR"

# Ask for subdomain name
read -p "Enter subdomain name (default: alice): " SUBDOMAIN
SUBDOMAIN=${SUBDOMAIN:-alice}

# Register subdomain
print_step "Registering subdomain: $SUBDOMAIN.$ROOT_NAME"
thru --json nameservice register-subdomain \
    "$SUBDOMAIN" \
    "$ROOT_REGISTRAR" \
    | tee /tmp/subdomain.json

# Extract domain account
DOMAIN_ACCT=$(jq -r '.nameservice_register_subdomain.domain_account' /tmp/subdomain.json)
print_step "Domain account: $DOMAIN_ACCT"

# Ask for records
read -p "Enter URL for domain record (default: https://mztacat.xyz): " DOMAIN_URL
DOMAIN_URL=${DOMAIN_URL:-https://mztacat.xyz}

read -p "Enter Twitter handle for domain record (default: @mztacat): " TWITTER_HANDLE
TWITTER_HANDLE=${TWITTER_HANDLE:-@mztacat}

# Add records
print_step "Adding URL record..."
thru --json nameservice append-record "$DOMAIN_ACCT" url "$DOMAIN_URL"

print_step "Adding Twitter record..."
thru --json nameservice append-record "$DOMAIN_ACCT" com.twitter "$TWITTER_HANDLE"

print_step "Adding Thru pubkey record..."
thru --json nameservice append-record "$DOMAIN_ACCT" thru.pubkey "$YOUR_PUBKEY"

# ==========================================
# SECTION 9: VERIFICATION
# ==========================================
print_section "VERIFICATION"

# Resolve records
print_step "Resolving URL record..."
thru --json nameservice resolve "$DOMAIN_ACCT" --key url

print_step "Resolving Twitter record..."
thru --json nameservice resolve "$DOMAIN_ACCT" --key com.twitter

print_step "Listing all records..."
thru --json nameservice list-records "$DOMAIN_ACCT"

# ==========================================
# SECTION 10: SAVE STATE
# ==========================================
print_section "SAVING STATE"

# Create state file
cat > /tmp/mztacat-thru-state.md << EOF
# MZTACAT Thru Alphanet State

## Identity
- Public Key: $YOUR_PUBKEY

## Program
- Project Name: $PROJECT_NAME
- Project Directory: $PROJECT_DIR/$PROJECT_NAME
- Meta Account: $META_ACCOUNT
- Buffer Account: $BUFFER_ACCOUNT

## Token
- Ticker: $TOKEN_TICKER
- Decimals: $TOKEN_DECIMALS
- Mint Seed: $MINT_SEED
- Mint Address: $MINT
- Account Seed: $ACCT_SEED
- Token Account: $TOKEN_ACCT
- Minted Amount: $MINT_AMOUNT $TOKEN_TICKER ($MINT_AMOUNT_UNITS micro-units)

## Name Service
- Root Name: $ROOT_NAME
- Registrar: $ROOT_REGISTRAR
- Subdomain: $SUBDOMAIN.$ROOT_NAME
- Domain Account: $DOMAIN_ACCT
- Records:
  - url: $DOMAIN_URL
  - com.twitter: $TWITTER_HANDLE
  - thru.pubkey: $YOUR_PUBKEY

## Commands Reference
- Check balance: \`thru --json getbalance $YOUR_PUBKEY\`
- Check token balance: \`thru --json getaccountinfo $TOKEN_ACCT\`
- Resolve domain: \`thru --json nameservice resolve $DOMAIN_ACCT --key url\`
- List all records: \`thru --json nameservice list-records $DOMAIN_ACCT\`
EOF

print_step "State information saved to /tmp/mztacat-thru-state.md"

# Display final message
echo -e "\n${PURPLE}"
echo "╔═══════════════════════════════════════════════════════════╗"
echo "║                                                           ║"
echo "║           🐱 MZTACAT SETUP COMPLETE! 🐱                  ║"
echo "║                                                           ║"
echo "║   Your Thru Alphanet environment is now configured with:  ║"
echo "║   • A deployed C program                                 ║"
echo "║   • A custom $TOKEN_TICKER token with $MINT_AMOUNT units     ║"
echo "║   • A name service domain: $SUBDOMAIN.$ROOT_NAME              ║"
echo "║                                                           ║"
echo "║   State information has been saved to:                    ║"
echo "║   /tmp/mztacat-thru-state.md                             ║"
echo "║                                                           ║"
echo "╚═══════════════════════════════════════════════════════════╝"
echo -e "${NC}"

# Display the state file
cat /tmp/mztacat-thru-state.md
```

### Make it executable:
```
chmod +x mztacat-thru-setup.sh
```

**Run it:**
```
./mztacat-thru-setup.sh
```

### Troubleshooting Reference
**Common errors at a glance**

| # | Error | Cause | Fix |
|---|-------|-------|-----|
| 1 | `jq: command not found` | jq not installed | `sudo apt install -y jq` |
| 2 | `thru: command not found` | npm global bin not in PATH | Add npm bin to PATH |
| 3 | `Key 'default' already exists` | Key already generated | Use existing key or `--overwrite` |
| 4 | `Failed to fetch latest release: HTTP 403` | GitHub API rate limit | Wait 30 min or download directly |
| 5 | Using someone else's pubkey | Copy-pasted from tutorial | Use YOUR pubkey from `account create` |
| 6 | Running out of gas | Balance too low | `thru faucet withdraw default 10000` |
| 7 | Toolchain download times out | 1.1GB file, short timeout | Use `curl --max-time 540` or `wget -c` |
| 8 | Wrong extraction path | Missing `--strip-components=1` | Re-extract with the flag |
| 9 | `make: No targets specified` | Not in project directory | `cd ~/thru-projects/my-first-thru-program` |
| 10 | `riscv64-unknown-elf-gcc: command not found` | Toolchain not installed | See Section 7 |
| 11 | `Error: Upload file not found` | Wrong working directory | `cd` into project dir first |
| 12 | Upload fails partway | Insufficient funds | Refill from faucet, retry (CLI resumes) |
| 13 | Empty mint address after extraction | jq not installed or wrong field | Install jq, use correct field name |
| 14 | Seed mismatch in response | CLI normalizes input seed | Not an error — use `mint_account` |
| 15 | `authority mismatch` on mint-to | Wrong authority argument | Use YOUR pubkey as authority |
| 16 | `root name already exists` | Root name taken | Use unique root name |
| 17 | `subdomain already exists` | Subdomain taken | Pick a different subdomain |

---

### Tips 
**General debugging tips
- [x] **Always use **--json** for machine-readable output
- [x] Always install jq first — it's the #1 source of broken-pipe errors
- [x] Check your balance before each major operation — thru --json getbalance $YOUR_PUBKEY
- [x] Save your seeds — without them you can't derive addresses later
- [x] Back up ~/.thru/cli/config.yaml — it contains your private key in plaintext

---

### Credits
This tutorial was researched, written, and tested by **MZTACAT**.
If you found this guide useful, consider following for more blockchain infrastructure, node operations, smart contract, and ecosystem tutorials.

## Connect

- **Website:** https://mztacat.xyz
- **X (Twitter):** https://x.com/mztacat
- **Telegram:** https://t.me/mztacat
- **GitHub:** https://github.com/mztacat


### Support My Work
If this guide helped you, a ⭐ on the repository and sharing it with others is greatly appreciated.
Feedback, corrections, and pull requests are always welcome.

---
**Official Links**
* Website: https://thru.org
* Documentation: https://docs.thru.org
* X (Twitter): https://x.com/thru_xyz
* Careers: https://jobs.ashbyhq.com/unto-labs

---
**Made with ❤️ by MZTACAT**
