----



<img width="2308" height="1230" alt="image" src="https://github.com/user-attachments/assets/3988ccaf-cdf7-4a21-8a89-c4f418282f23" />

---

### What is Thru 
Thru: A high-performance L1 built by Unto Labs, which is focused on ultra-fast transactions and low latency.  It runs on ThruVM, a custom VM built on the RISC-V instruction set (same open standard used outside crypto in chips from NVIDIA, Qualcomm, etc.)
* Funding: $14.4M led by Framework Ventures and Electric Capital
* Languages: Write smart contracts in C, C++, Rust, or any LLVM-supported language

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

----
## Getting Started 
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
npm install -g thru
```
<img width="1676" height="322" alt="image" src="https://github.com/user-attachments/assets/c211f6d2-3e1a-439b-9e16-d99be1180eb8" />

### Verify Thru CLI
```
thru --version
# Expected: thru 0.2.38+8eb269bd  (or newer)
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












---
## Official links:

Website: https://thru.org

Docs: https://docs.thru.org

X: https://x.com/thru_xyz

Jobs: https://jobs.ashbyhq.com/unto-labs

---
