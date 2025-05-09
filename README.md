
# Bitcoin Multisig Guide

## 05/02

## 05/09

## Table of Contents
1. [Multisig Security Concepts](#multisig-security-concepts)
2. [XPUBs, Public Keys, and Private Keys](#xpubs-public-keys-and-private-keys)
3. [Multisig Worst-Case Scenarios and Recovery](#multisig-worst-case-scenarios-and-recovery)

---

## Multisig Security Concepts

### What is Multisig?
Multisig (short for multi-signature) is a method of securing Bitcoin using multiple keys. A common setup is 2-of-3, where three keys exist and any two are needed to sign a transaction.

### Security Advantages
- **Theft Mitigation:** A thief needs multiple keys to steal funds.
- **Loss Resilience:** One key can be lost, but funds are still recoverable.
- **Geographical Distribution:** Keys can be stored in different locations to reduce single points of failure.

### Design Patterns
| Model           | Description                                      | Threat Mitigation |
|----------------|--------------------------------------------------|-------------------|
| 2-of-3 Solo     | User controls all 3 keys                         | Key theft         |
| 2-of-3 Partner  | User holds 2 keys, a trusted party holds 1      | Key loss          |
| 2-of-3 Collaborative Custody | User + wallet provider + third party | Custody risk, theft |

### Security Tips
- Store each key in a different geographic or digital location.
- Use hardware wallets whenever possible.
- Don't reuse the same seed for multiple cosigners.
- Always keep an up-to-date backup of your descriptor or wallet configuration.

---

## XPUBs, Public Keys, and Private Keys

### The Hierarchy: From Mnemonic to Multisig Script

```plaintext
Mnemonic (Seed)
    ↓
Master Private Key (m)
    ↓
Extended Private Key (xprv) — gives access to all derived private keys
    ↓
Extended Public Key (xpub) — can generate addresses but not sign
```

### Cosigner Key Roles
Each multisig cosigner contributes an extended public key (XPUB).
- **xpub:** Extended Public Key; safe to share for address generation
- **xprv:** Extended Private Key; must be kept secret

### How Public Keys Fit Into Multisig
A 2-of-3 multisig script looks like this:
```
2 <pubkey1> <pubkey2> <pubkey3> 3 OP_CHECKMULTISIG
```
This script requires signatures from any 2 of the 3 public keys.

### XPUBs in Descriptors
Descriptors express how addresses are constructed. Example:
```
wsh(multi(2,[fingerprint1/derivation1]xpub1/0/*,[fingerprint2/derivation2]xpub2/0/*,[fingerprint3/derivation3]xpub3/0/*))
```
This allows wallets like Sparrow to generate the same set of addresses independently.

---

## Multisig Worst-Case Scenarios and Recovery

### Scenario 1: Lost Access to Sparrow Wallet
- **What happens:** You can’t open the wallet or the app anymore.
- **Solution:**
    - Multisig is not tied to Sparrow — it’s based on BIP-39 seeds and descriptors.
    - Reinstall Sparrow and re-import your wallet (using the descriptor or cosigner xpubs + seed).
    - Or import your seed(s) into any other Bitcoin wallet that supports multisig (e.g., Electrum, Nunchuk, Specter).
    - Worst case: use a Bitcoin script interpreter or command-line tool (e.g., bitcoin-cli, miniscript) to construct and sign manually.
    - As long as you have access to 2 of 3 seed phrases and the wallet descriptor or cosigner xpubs, you're good.
- **Prevention:** Always back up descriptors and seed phrases securely, ideally with geographical redundancy.

### Scenario 2: One Seed Phrase Lost
- **What happens:** One of the cosigner seed phrases is gone.
- **Solution:**
    - No problem — multisig requires only 2 of 3.
    - Just make sure the remaining 2 seeds are backed up safely.
    - You can create a new multisig wallet using the 2 remaining cosigners and generate a new 2-of-3 wallet to migrate funds for better redundancy.
- **Prevention:** Keep backups of all seed phrases in multiple secure locations. Consider using a third-party storage service for redundancy.

### Scenario 3: Two Seed Phrases Lost
- **What happens:** Only one cosigner remains.
- **Risk Level:** Critical
- **Solution:** Funds are permanently locked — a 2-of-3 wallet cannot be spent with only one signature.
- **Mitigation:**
    - This is the risk you balance with 2-of-3: losing 2 keys = funds gone.
    - Always keep at least 3 geographically distributed backups of each cosigner (paper, hardware, metal, etc.).
    - Use Shamir's Secret Sharing or multisig with trusted parties if you're worried about this.
- **Prevention:** Store all seed phrases in separate, secure locations. Implement Shamir’s Secret Sharing or use 3rd parties for further safety.

### Scenario 4: One Seed + Wallet Descriptor Lost
- **What happens:** You have 2 seed phrases, but you don’t remember how the multisig wallet was set up (no descriptor or xpubs saved).
- **Solution:**
    - You can reconstruct the descriptor manually if you know:
        - The script type (wsh, sh(wsh(...)), etc.)
        - The derivation paths used
        - Which xpubs were involved and in what order
    - This is painful, so always save the full descriptor string when you create the wallet. Sparrow gives it to you — back it up like a seed!
- **Prevention:** Save the full descriptor string when setting up your wallet and store it securely.

### Scenario 5: Two Hardware Wallets Fail
- **What happens:** You used 2 hardware wallets, and both are bricked.
- **Solution:**
    - If you backed up the recovery seed phrases, you can restore them into:
        - Another hardware wallet
        - Sparrow
        - Electrum
        - BlueWallet, etc.
    - If you didn’t back up the seeds... then it’s like scenario 3: funds are lost.
- **Prevention:** Always back up the seed phrases of your hardware wallets in multiple secure locations.

### Scenario 6: One Seed Compromised (Stolen)
- **What happens:** An attacker gets access to one seed.
- **Solution:**
    - Funds are still safe — attacker needs two seeds to steal anything.
    - You can migrate funds to a new 2-of-3 multisig setup (with new keys) as a precaution.
    - Multisig allows rolling over to new sets of cosigners — just spend everything to a fresh setup.
- **Prevention:** Immediately migrate funds to a new multisig setup with fresh keys.

---

## Final Notes
- Always test your multisig setup before storing significant funds.
- Document and share recovery instructions with trusted individuals (if appropriate).
- Consider using N-of-M setups (e.g., 3-of-5) for institutional use.
