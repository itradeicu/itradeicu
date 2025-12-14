# Secure Wallet Source Code

> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.


---

This article is produced by the [https://itrade.icu](https://www.itrade.icu) Quantitative Trading Lab.  
This project provides a secure **crypto wallet creation and decryption tool**, enabling users to generate their own blockchain wallets while ensuring encrypted storage of private keys and passwords.

## Introduction
* **Wallet Creation**: The `create.js` script allows users to generate a wallet, automatically creating an encrypted private key, decryption key, and wallet password. Supports random generation or custom passwords.
* **Wallet Decryption**: The `unravel.js` script enables secure decryption of the wallet file to view and use the private key.
* **Security Management**: Offers multiple wallet backup and storage recommendations to prevent asset loss due to private key loss.
* **Dependency Management**: Supports dependency installation via `npm`, `pnpm`, or `yarn`, making it easy to use.

This tool is suitable for personal use or learning about blockchain wallet encryption and decryption mechanisms. It emphasizes operational security, recommending decryption in an offline environment to ensure private key safety.

## Encryption and Decryption
+ `create.js`: Encrypts the wallet
+ `unravel.js`: Decrypts the wallet

## Dependency Installation
Choose your preferred method for downloading dependencies; `pnpm` is recommended.
```bash
npm install 
pnpm install 
yarn install 
```
---
## Wallet Creation
Run the following command to create a wallet:
```bash
node create.js
```

### Two Optional Methods for Wallet Creation
1. Create a wallet using a random password:
```bash
(base) loser@loserdeMacBook-Pro safe-wallet % node create.js 
✔ Enter a custom KEY value, or input R for random creation. · r
✔ Enter a custom IV value, or input R for random creation. · r
✔ Enter a custom PASSWORD value, or input R for random creation. · r
{ key: 'sBIIY)R?(3b&', iv: 'D.Tw%<AOuYff', password: 'UL6GT4H+9q*6' }
Wallet created successfully: 0xC640DbfE7CE8B30497276BF72604f0A4b78A5d0B
```
---

2. Create a wallet using a custom password:
```bash
(base) loser@loserdeMacBook-Pro safe-wallet % node create.js
✔ Enter a custom KEY value, or input R for random creation. · 123123
✔ Enter a custom IV value, or input R for random creation. · 123123
✔ Enter a custom PASSWORD value, or input R for random creation. · 123123
{ key: '123123', iv: '123123', password: '123123' }
Wallet created successfully: 0x256823E8DD9EE91B4ac9C259B350C85244bF2c97
```
### Verification After Creation
Upon successful creation, three files will be generated in the `./address` directory: `*.key.iv`, `*.password`, and `*.wallet`, corresponding to the encrypted wallet private key, decryption key, and wallet password, respectively.  
![alt text](/codes/welfare/safe-wallet_1.png)

### Security Recommendations
It is recommended to back up the wallet private key in multiple locations to prevent loss, as a lost private key cannot be recovered.
1. Use [Tencent Cloud OSS](https://buy.cloud.tencent.com/cos) (3.9¥/5 years) for storage, or save in multiple locations to avoid loss.  
![alt text](/codes/welfare/safe-wallet_2.png)
2. Store in cloud drives, such as Baidu Netdisk, Alibaba Cloud Disk, etc.
3. Store on external hard drives, such as Western Digital, Kingston, etc.
4. Security Reminder: Avoid relying on a single storage method. Instead, save copies in multiple locations to prevent asset loss due to a single point of failure.

## Wallet Decryption
Run the following command to decrypt the wallet:
1. Fill in the corresponding wallet details in `unravel.js` for the relevant variables:
```bash
# Enter the corresponding file names based on the wallet's suffix
const key_iv = ""
const password = ""
const wallet = ""
```
2. Run the following command to decrypt the wallet:
```bash
node unravel.js
```
> You can then view your wallet.  
![alt text](/codes/welfare/safe-wallet_3.png)

### Security Recommendations
1. Install dependencies before running the decryption.
2. Run the decryption process **offline** to prevent hackers from accessing your wallet private key.
3. Copy the wallet private key in segments to avoid it being intercepted by hackers.

## Secure Wallet Source Code
<Articles />