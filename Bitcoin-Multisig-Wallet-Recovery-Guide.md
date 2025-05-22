# Bitcoin Multisig Wallet Recovery Guide

This guide outlines how to recover a 2-of-3 multisig wallet in Sparrow Wallet using devices, xpubs, seed phrases, restored devices after loss/wipe, or a wallet descriptor (QR code or string). The wallet uses two Jade devices and a Coldcard Q.

## Prerequisites
- **Devices**: Coldcard Q and two Jade devices with seed phrases and optionally the multisig configuration.
- **xpubs**: Extended public keys for all three devices, with derivation path (e.g., `m/48'/0'/0'/2'` for mainnet) and master fingerprints.
- **Seed Phrases**: 12/24-word BIP-39 seed phrases for all three devices.
- **Backups**: Exported Sparrow wallet file and/or wallet descriptor (QR code, string, or file).
- **Network**: Bitcoin mainnet or testnet (use testnet for practice).

## 1. Deleting the Multisig Wallet
1. In Sparrow, select the wallet and go to **File** > **Delete Wallet**.
2. Confirm deletion. This removes the wallet from Sparrow’s database but does not affect blockchain funds or device configurations.
3. **Backup Before Deletion**:
   - Go to **Settings** > **Export** > **Export File** > **Sparrow Wallet** or **BSMS** to save the wallet file.
   - Go to **Settings** > **Export** > **Output Descriptor** to save the descriptor as a QR code, text string, or `.txt` file.
   - Store backups securely (e.g., microSD card, encrypted USB, metal plate).

## 2. Recovering Using Devices
**Requirements**: Devices must have the multisig configuration stored (imported during setup).
1. **Verify Configuration**:
   - **Coldcard Q**: Go to **Settings** > **Multisig Wallets**, select the wallet, and verify details (2-of-3 policy, xpubs, derivation path).
   - **Jade**: Check wallet settings via Blockstream Green or Sparrow to confirm multisig details.
2. **Export Configuration**:
   - **Coldcard Q**: From **Multisig Wallets**, export via QR code or microSD (`.json`/`.txt`).
   - **Jade**: Export via QR code or companion app (e.g., Blockstream Green).
3. **Import into Sparrow**:
   - Go to **File** > **New Wallet**, name it, and set **Policy Type** to **Multi Signature** (2 of 3).
   - Choose **Native Segwit** (derivation: `m/48'/0'/0'/2'` for mainnet).
   - For each keystore, select **Airgapped Hardware Wallet**, scan the QR code, or import the file.
   - Click **Apply** to recreate the wallet.
4. **Verify**: Check **Addresses** and **Transactions** tabs to confirm addresses and balance match the original wallet.

## 3. Recovering Using xpubs
**Requirements**: xpubs for all three devices, derivation path, and master fingerprints.
1. **Gather xpubs**:
   - **Coldcard Q**: Go to **Settings** > **Multisig Wallets** > **Export XPUB** or **Address Explorer** > **Export XPUB** (path: `m/48'/0'/0'/2'`).
   - **Jade**: Export xpub via Blockstream Green or Sparrow (same path).
   - Save xpubs via QR code or microSD.
2. **Import into Sparrow**:
   - Go to **File** > **New Wallet**, name it, and set **Policy Type** to **Multi Signature** (2 of 3).
   - Choose **Native Segwit**.
   - For each keystore, select **xPub / Watch Only Wallet**, paste/scan the xpub, and set the derivation path and fingerprint.
   - Click **Apply** to create a watch-only wallet.
3. **Verify**: Confirm addresses and balance. To spend, sign with two devices.
4. **Repeat**: Delete via **File** > **Delete Wallet** and re-import xpubs to test.

## 4. Recovering Using Seed Phrases
**Requirements**: All three seed phrases, derivation path, and script type. Use an airgapped computer for security.
1. **Derive xpubs**:
   - Use an offline BIP39 tool (e.g., Ian Coleman’s BIP39 Tool, https://iancoleman.io/bip39/).
   - Enter each seed phrase, select **Native Segwit**, and set derivation path (`m/48'/0'/0'/2'`).
   - Copy the xpub and master fingerprint for each device.
2. **Import into Sparrow**:
   - Follow the xpub import steps above, using the derived xpubs.
   - Alternatively, import two seed phrases directly:
     - Select **New or Imported Software Wallet** for two keystores.
     - Enter the seed phrases and set the derivation path.
     - Use the third device’s xpub for the watch-only keystore.
   - Click **Apply** to create the wallet.
3. **Verify**: Confirm addresses and balance. Test signing with two seed phrases or devices.
4. **Repeat**: Delete and re-import to test.

## 5. Recovering After Losing or Wiping All Devices
**Requirements**: All three seed phrases, derivation path (e.g., `m/48'/0'/0'/2'`), and script type (e.g., Native Segwit). New or wiped devices (Coldcard Q, two Jades).
1. **Restore Seed Phrases on Devices**:
   - **Coldcard Q**:
     - On a new or wiped Coldcard Q, select **Import Existing** > **Seed Phrase**.
     - Enter the 12/24-word seed phrase (include BIP-39 passphrase if used).
     - Set a new PIN and confirm.
   - **Jade Devices**:
     - On each new or wiped Jade, select **Recover Wallet**.
     - Enter the 12/24-word seed phrase and complete setup (e.g., set PIN).
2. **Derive xpubs**:
   - **Coldcard Q**: Go to **Settings** > **Multisig Wallets** > **Export XPUB** or **Address Explorer** > **Export XPUB** (path: `m/48'/0'/0'/2'`). Export via QR code or microSD.
   - **Jade**: Use Blockstream Green or Sparrow to export the xpub (same path) via QR code or USB.
   - Note the master fingerprint for each xpub.
3. **Import into Sparrow**:
   - Go to **File** > **New Wallet**, name it (e.g., “Recovered-2of3-Lost-Devices”), and set **Policy Type** to **Multi Signature** (2 of 3).
   - Choose **Native Segwit**.
   - For each keystore, select **xPub / Watch Only Wallet**, scan/paste the xpub, and set the derivation path and fingerprint.
   - Click **Apply** to create the watch-only wallet.
4. **Optional: Import Multisig Configuration to Devices**:
   - In Sparrow, export the multisig configuration (**Settings** > **Export** > **Coldcard Multisig** or **BSMS**) to a microSD card or QR code.
   - On Coldcard Q, go to **Settings** > **Multisig Wallets** > **Import from SD/QR**.
   - On Jade, import via Blockstream Green or Sparrow.
5. **Verify**: Confirm addresses match the original wallet. Test signing a transaction with two restored devices (e.g., Coldcard Q and one Jade).
6. **Repeat**: Delete via **File** > **Delete Wallet** and re-import to test.

## 6. Recovering Using Wallet Descriptor (QR Code or String)
**Requirements**: Wallet descriptor (QR code, text string, or `.txt` file) exported from Sparrow, containing xpubs, derivation path, script type, and 2-of-3 policy.
1. **Export Descriptor (Before Loss)**:
   - In Sparrow, go to **Settings** > **Export** > **Output Descriptor**.
   - Choose **Show QR** to display as a QR code, **Copy** to copy the text string, or **Export File** to save as a `.txt` file.
   - Example descriptor: `wsh(multi(2,[12345678/48'/0'/0'/2']zpub...,[87654321/48'/0'/0'/2']zpub...,[abcdef12/48'/0'/0'/2']zpub...))`.
   - Store securely (e.g., paper, metal plate, encrypted USB).
2. **Import Descriptor into Sparrow**:
   - Go to **File** > **New Wallet**, name it (e.g., “Recovered-2of3-Descriptor”).
   - Select **Output Descriptor** in the **Keystores** section.
   - Choose:
     - **Scan QR**: Scan the descriptor QR code with your computer’s camera.
     - **Paste String**: Paste the descriptor text string.
     - **Import File**: Select the `.txt` file from a microSD card or USB.
   - Sparrow parses the descriptor, populating script type, policy, xpubs, and derivation paths.
   - Verify details (2-of-3 policy, derivation path `m/48'/0'/0'/2'`, master fingerprints).
   - Click **Apply** to create the watch-only wallet.
3. **Sync and Verify**:
   - Sparrow syncs with the blockchain to display addresses, transactions, and balance.
   - Check **Addresses** or **Receive** tab to confirm addresses match the original wallet.
   - Verify transactions in the **Transactions** tab.
4. **Sign Transactions**:
   - Use two devices (Coldcard Q, one Jade) to sign PSBTs exported from Sparrow via QR code or microSD.
   - If devices are lost, restore seed phrases on new devices (see Section 5).
5. **Optional: Import to Devices**:
   - Export the multisig configuration from Sparrow (**Settings** > **Export** > **Coldcard Multisig** or **BSMS**).
   - Import to Coldcard Q (**Settings** > **Multisig Wallets** > **Import from SD/QR**) and Jade (via Blockstream Green or Sparrow).
6. **Repeat**: Delete via **File** > **Delete Wallet** and re-import the descriptor to test.

## Security Notes
- **Backups**: Store seed phrases, xpubs, wallet file, and descriptor securely (e.g., metal plates, encrypted USBs).
- **Testnet**: Practice on Bitcoin testnet to avoid risking real funds.
  - In Sparrow, use **Tools** > **Restart in Testnet**.
  - On Coldcard Q, set **Settings** > **Blockchain** > **Testnet**.
  - On Jade, use testnet mode in Blockstream Green.
  - Use testnet derivation path: `m/48'/1'/0'/2'`.
- **Airgapped**: Restore seed phrases, derive xpubs, and import descriptors on airgapped devices or computers to prevent exposure.
- **Verification**: Verify addresses on at least two devices before sending funds.
- **Derivation Path**: Ensure the correct path (e.g., `m/48'/0'/0'/2'` for mainnet, `m/48'/1'/0'/2'` for testnet) and script type (e.g., Native Segwit, P2WSH).

## Testing Multiple Recoveries
- Delete the wallet after each recovery via **File** > **Delete Wallet**.
- Repeat each method (devices, xpubs, seed phrases, restored devices, descriptor) to confirm reliability.
- Use testnet for all tests to ensure safety.