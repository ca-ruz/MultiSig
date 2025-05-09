
# Bitcoin Multisig Guide

## 05/02

## 05/09

## Table of Contents
1. [Multisig Security Concepts](#multisig-security-concepts)
2. [XPUBs, Public Keys, and Private Keys](#xpubs-public-keys-and-private-keys)
3. [Keys and Address Generation](#keys-and-address-generation)
4. [Multisig Worst-Case Scenarios and Recovery](#multisig-worst-case-scenarios-and-recovery)

---

## Multisig Security Concepts

### What is Multisig?
Multisig (multi-signature) secures Bitcoin with multiple keys. A 2-of-3 setup uses three keys, requiring any two to sign a transaction.

### Security Advantages
- **Theft Mitigation:** Needs multiple keys to steal funds.
- **Loss Resilience:** One lost key doesn’t lock funds.
- **Geographical Distribution:** Store keys in different locations to avoid single-point failures.

### Design Patterns
| Model           | Description                              | Threat Mitigation |
|----------------|------------------------------------------|-------------------|
| 2-of-3 Solo     | User holds all 3 keys                    | Key theft         |
| 2-of-3 Partner  | User holds 2, trusted party holds 1      | Key loss          |
| 2-of-3 Collaborative | User + provider + third party       | Custody risk, theft |

### Security Tips
- Store keys in separate locations.
- Use hardware wallets.
- Avoid seed reuse across cosigners.
- Back up descriptors or wallet configs.

---

## XPUBs, Public Keys, and Private Keys

### Key Hierarchy

```plaintext
Mnemonic (Seed)
    ↓
Master Private Key
    ↓
Extended Private Key (xprv) — controls all derived keys
    ↓
Extended Public Key (xpub) — generates addresses, can’t sign
```

### Cosigner Roles
Each cosigner provides an XPUB.
- **xpub:** Safe to share for address generation.
- **xprv:** Keep secret; controls funds.

### Public Keys in Multisig
A 2-of-3 multisig script:
```
2 <pubkey1> <pubkey2> <pubkey3> 3 OP_CHECKMULTISIG
```
Requires two signatures from the three public keys.

### XPUBs in Descriptors
Descriptors define address creation, e.g.:
```
wsh(multi(2,[fingerprint1/derivation1]xpub1/0/*,[fingerprint2/derivation2]xpub2/0/*,[fingerprint3/derivation3]xpub3/0/*))
```
Enables wallets like Sparrow to generate addresses.

### XPUBs vs. Public Keys
**Public Key:**
- Part of a key pair; generates one Bitcoin address.
- Safe to share for receiving funds.

**XPUB (Extended Public Key):**
- Generates multiple public keys/addresses in HD wallets (BIP-32/BIP-44).
- Safe to share, but reveals all derived addresses (not funds).

**Key Differences:**
- **Functionality:** Public key = one address; XPUB = many addresses.
- **Use Case:** Public keys for single addresses; XPUBs for HD wallets.
- **Security:** Both safe to share, but XPUBs reduce privacy by exposing all addresses.

**Summary:** XPUBs extend public keys to generate multiple addresses, ideal for wallets managing many addresses.

### XPRVs vs. Private Keys
**Private Key:**
- Part of a key pair; signs transactions for one address.
- Must stay secret to protect funds.

**XPRV (Extended Private Key):**
- Generates multiple private keys/addresses in HD wallets (BIP-32/BIP-44).
- Must stay secret; controls all derived funds.

**Key Differences:**
- **Functionality:** Private key = one address; XPRV = many private keys.
- **Use Case:** Private keys for signing; XPRVs for HD wallet management.
- **Security:** Both must be secret; XPRV exposure risks all derived funds.

**Summary:** XPRVs extend private keys to manage multiple addresses securely in HD wallets.

---

## Keys and Address Generation

### General Address Generation
Bitcoin addresses are created from keys, for both single-signature and multisig wallets:

**Private to Public Key:**
- Private key (random number) generates a public key via elliptic curve math (secp256k1).
- Private key signs transactions; public key creates addresses.

**Public Key to Address:**
- Public key is hashed (SHA-256, RIPEMD-160) and encoded (Base58Check or Bech32) to form an address.
- Addresses are shared to receive funds; safe as they hide keys.

**XPRV and XPUB in HD Wallets:**
- XPRV (from mnemonic seed) generates private keys for signing.
- XPUB (from XPRV) generates public keys for addresses.
- HD wallets (BIP-32/BIP-44) create many addresses from one XPRV/XPUB.

**Security:**
- Keep XPRVs/private keys secret.
- XPUBs/public keys are shareable but may reduce privacy.
- Back up mnemonic seeds securely.

### Multisig Address Generation
Multisig addresses combine multiple keys:

**Public Keys:**
- Each cosigner provides a public key (from XPUB).
- Combined into a multisig script, e.g.:
  ```
  2 <pubkey1> <pubkey2> <pubkey3> 3 OP_CHECKMULTISIG
  ```
- Script is hashed to create a multisig address (P2SH/P2WSH).

**XPUBs:**
- Cosigners share XPUBs to generate public keys for multisig scripts.
- Descriptor example:
  ```
  wsh(multi(2,[fingerprint1/derivation1]xpub1/0/*,[fingerprint2/derivation2]xpub2/0/*,[fingerprint3/derivation3]xpub3/0/*))
  ```

**XPRVs and Private Keys:**
- XPRVs generate private keys for signing.
- Two private keys (from XPRVs) sign multisig transactions.

**Key Relationships:**
- XPRV → Private keys for signing.
- XPUB → Public keys for multisig scripts.
- Public keys → Multisig address via script hashing.
- Private keys → Sign transactions.

**Security:**
- Store XPRVs/private keys securely (e.g., hardware wallets).
- Share XPUBs cautiously to limit address visibility.
- Back up descriptors to recover wallet setup.
- Test multisig with small amounts first.

---

## Multisig Worst-Case Scenarios and Recovery

### Scenario 1: Lost Access to Sparrow Wallet
- **What happens:** Can’t access wallet/app.
- **Solution:**
    - Reinstall Sparrow, import descriptor or xpubs + seed.
    - Use other multisig wallets (Electrum, Nunchuk, Specter).
    - Worst case: Sign manually with bitcoin-cli or miniscript.
    - Need 2 of 3 seeds + descriptor/xpubs.
- **Prevention:** Back up descriptors/seeds with redundancy.

### Scenario 2: One Seed Phrase Lost
- **What happens:** One cosigner seed is gone.
- **Solution:**
    - Still safe; only 2 of 3 needed.
    - Back up remaining seeds.
    - Optionally, create new 2-of-3 wallet and migrate funds.
- **Prevention:** Store seed backups in multiple locations.

### Scenario 3: Two Seed Phrases Lost
- **What happens:** Only one cosigner remains.
- **Risk Level:** Critical
- **Solution:** Funds locked; 2-of-3 requires two signatures.
- **Mitigation:** Use Shamir’s Secret Sharing or trusted parties.
- **Prevention:** Store seeds in separate, secure locations.

### Scenario 4: One Seed + Descriptor Lost
- **What happens:** Have 2 seeds but no descriptor/xpubs.
- **Solution:**
    - Reconstruct descriptor with script type, derivation paths, xpub order.
    - Save descriptor at wallet creation.
- **Prevention:** Back up descriptor securely.

### Scenario 5: Two Hardware Wallets Fail
- **What happens:** Two hardware wallets are bricked.
- **Solution:**
    - Restore seeds to new hardware or software wallets (Sparrow, Electrum).
    - No seed backups = funds lost (like Scenario 3).
- **Prevention:** Back up seeds in multiple secure locations.

### Scenario 6: One Seed Compromised
- **What happens:** Attacker has one seed.
- **Solution:**
    - Funds safe; needs two seeds.
    - Migrate to new 2-of-3 setup with fresh keys.
- **Prevention:** Migrate funds immediately if compromised.

---

## Final Notes
- Test multisig setups with small amounts.
- Document recovery steps for trusted contacts.
- Consider N-of-M setups (e.g., 3-of-5) for institutional use.
