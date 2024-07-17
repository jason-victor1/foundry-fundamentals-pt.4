# Understanding Blockchain Transactions

## Transaction Details
A transaction captures details of an activity that has taken place on a blockchain. Foundry saved all my blockchain interactions in specific folders:

- **Broadcast Folder**: Foundry saved all my blockchain interactions here.
- **Dry-Run Folder**: Used for interactions I made when I didn't have a blockchain running (e.g., deploying a contract without specifying an `--rpc-url`).

## Chain ID
- **Chain ID**: A unique identifier assigned to a specific blockchain network. It distinguished one blockchain from another and was crucial for ensuring the security and integrity of transactions and interactions.

## Viewing Deployment Details
1. **Explorer Tab**: On the left side of my screen, in the Explorer tab, I found the `broadcast` folder.
2. **run-latest.json**: Clicking on `run-latest.json`, I found more details about the last deployment script. It showed transactionType, contractName, and contractAddress.

## Transaction Section Breakdown
Example Transaction:
```json
"transaction": {
  "from": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266",
  "to": null,
  "gas": "0x714e1",
  "value": "0x0",
  "input": "0x608060...c63430008130033",
  "nonce": "0x0",
  "chainId": "0x7a69",
  "accessList": null,
  "type": null
}
```
- **from**: The address used to sign the transaction.
- **to**: The recipient, `null` or `address(0)` for new smart contract deployments.
- **gas**: The amount of gas spent. Hex value (e.g., `0x714e1`).
  - **Tip**: I converted hex to decimal using:
    ```bash
    cast --to-base 0x714e1 dec
    ```
- **value**: The transaction value or the amount of ETH sent. For contract deployment, this was `0x0`.
- **input**: The contract deployment code and the contract code (truncated in the example).
- **nonce**: A unique identifier assigned to each transaction sent from a specific account, ensuring each transaction is processed only once.
- **accessList**: Optimized the gas cost of transactions by listing addresses and storage keys likely to be accessed.
- **type**: Could be ignored for now.

## Additional Transaction Values
- **v, r, s**: Components of a transaction's signature, used to validate the authenticity and integrity of the transaction.

## Key Points
- Every time I changed the state of the blockchain, it was done using a transaction.
- The `data` field of a transaction indicated the change.
- Deployment bytecode, contract bytecode, and OPCODEs would be covered in a future lesson.

# Securing Private Keys: Best Practices

## Avoid Using `.env` for Private Keys
I never used a `.env` file to store private keys again.

## Encrypting Your Keys Using ERC2335
For demonstration, I pretended my private key was:
```plaintext
0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 (key 0 from Anvil)
```

## Importing and Encrypting My Key
1. **Import and Encrypt Key**:
   - I typed the following command in my terminal:
     ```bash
     cast wallet import nameOfAccountGoesHere --interactive
     ```
   - I was asked for my private key and a password to secure it. This process was done only once.

## Deploying with Encrypted Keys
In the last lesson, I deployed using:
```bash
forge script script/DeploySimpleStorage.s.sol --rpc-url $RPCURL --broadcast --private-key $PRIVATEKEY
```

Now that I had configured my wallet, I deployed as follows:
```bash
forge script script/DeploySimpleStorage.s.sol --rpc-url http://127.0.0.1:8545 --broadcast --account nameOfAccountGoesHere --sender 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
```
- I was prompted to enter my password. Deployment was not possible without the password.

## Managing Configured Wallets
- To see all the configured wallets, I used the command:
  ```bash
  cast wallet list
  ```

## Clearing Command History
I cleared my history to ensure my private key wasn't stored in plain text:
```bash
history -c
```

## Final Tips
- I stayed safe by ensuring my keys were always encrypted.
- If I saw my private key in plain text, I knew I was doing something wrong.

## Summary
- **Encrypt Private Keys**: I used `cast wallet import` to encrypt and secure my keys with a password.
- **Secure Deployment**: I deployed contracts using the `--account` option with my encrypted key.
- **Clear History**: I regularly cleared my command history to avoid leaving sensitive information.
- **Stay Vigilant**: I always ensured my private key was protected and never exposed in plain text.

# Interacting With Contract Addresses via Command Line & Foundry's Cast Tool

## Overview
This lesson built on previous lessons where I deployed `SimpleStorage` via `forge script`. I had Anvil running and the smart contract was deployed. I copied the contract address for further interaction.

## Sending Information to the Blockchain

### Using Cast
Foundry had an in-built tool known as **Cast**. It came loaded with numerous commands to interact with. I learned more about them by typing:
```bash
cast --help
```

### Sending Transactions
One useful command was `send`, which was designed to sign and publish a transaction. To view help about `send`, I typed:
```bash
cast send --help
```

To use `send`, I needed a signature and some arguments. I used the address I copy-pasted from my terminal. The address may be different from the example shown.

```bash
cast send 0x5FbDB2315678afecb367f032d93F642f64180aa3 "store(uint256)" 1337 --rpc-url $RPCURL --private-key $PRIVATEKEY
```

### Command Breakdown
- `cast send`: Command used to sign and publish my transaction.
- `0x5FbDB2315678afecb367f032d93F642f64180aa3`: Target contract address.
- `"store(uint256)"`: Signature of the function being called.
- `1337`: The number passed to the `store` function, fitting the `uint256` input.
- `--rpc-url $RPCURL`: RPC URL where the transaction was sent.
- `--private-key $PRIVATEKEY`: Private key used to sign the transaction.

## Reading Information from the Blockchain

### Using Cast Call
Cast provided a way to read information stored on the blockchain. To find out more, I typed:
```bash
cast call --help
```
It worked similarly to `send`, but it only read data without modifying it. 

```bash
cast call 0x5FbDB2315678afecb367f032d93F642f64180aa3 "retrieve()"
```
I received a hex value, for example:
```plaintext
0x0000000000000000000000000000000000000000000000000000000000000539
```
To convert this hex value to a normal number, I typed:
```bash
cast --to-base 0x0000000000000000000000000000000000000000000000000000000000000539 dec
```
The output was:
```plaintext
1337
```

## Practice
- I sent multiple transactions with different numbers.
- I read them from the blockchain to see the results.

## Summary
- **Sending Transactions**: I used `cast send` to interact with deployed contracts by sending data.
- **Reading Data**: I used `cast call` to read data from the blockchain without modifying it.
