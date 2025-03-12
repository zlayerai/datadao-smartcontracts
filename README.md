# Data Liquidity Pool (DLP)

## Table of Contents
1. [Introduction](#1-introduction)
2. [Overview](#2-overview)
3. [Flow](#3-flow)
4. [Installation](#4-installation)
5. [DLP Contracts](#5-dlp-contracts)
    - [DataLiquidityPool](#dataliquiditypool)
    - [DAT (Data Access Token)](#dat-data-access-token)


## 1. Introduction

Vana turns data into currency to push the frontiers of decentralized AI. It is a layer one blockchain designed for private, user-owned data. It allows users to collectively own, govern, and earn from the AI models trained on their data. For more context see this [docs](https://docs.vana.org/vana).

This repository is designed for DLP (Data Liquidity Pool) creators and developers who want to deploy a DLP and its associated DLP Token on the Vana blockchain. The code provided here serves as an example implementation of a DLP smart contract. It's important to note that this is just one possible implementation – developers are encouraged to customize this contract or create their own versions to suit their specific use cases. Whether you're looking to create a standard DLP or develop a highly customized one, this repo provides a template implementation.

## 2. Overview

### [Data Registry Contract](https://docs.vana.org/vana/core-concepts/key-elements/smart-contracts#data-registry-contract)

The data registry contract functions as a central repository for managing all data within the network, functioning as a comprehensive file catalog. It allows users to add new files to the system, with each file receiving a unique identifier for future reference.

The contract manages access control for these files, enabling file owners to grant specific addresses permission to access their files. It also handles the storage of file metadata, including any offchain proofs or attestations related to file validation, which can include various metrics such as authenticity, ownership, and quality scores. Users can retrieve detailed information about any file in the registry using its unique identifier, including its permissions and associated proofs.

Moksha (deprecated version): [0xEA882bb75C54DE9A08bC46b46c396727B4BFe9a5](https://moksha.vanascan.io/address/0xEA882bb75C54DE9A08bC46b46c396727B4BFe9a5)  
Moksha: [0x8C8788f98385F6ba1adD4234e551ABba0f82Cb7C](https://moksha.vanascan.io/address/0x8C8788f98385F6ba1adD4234e551ABba0f82Cb7C)


### [TEE Pool Contract](https://docs.vana.org/vana/core-concepts/key-elements/smart-contracts#tee-pool-contract)

The TEE Pool contract manages and coordinates the TEE Validators and serves as an escrow for holding fees associated with validation tasks. Users pay a fee to submit data for validation, and the contract ensures that the validators process the data and provide proof of validation.

Moksha (deprecated version): [0xF084Ca24B4E29Aa843898e0B12c465fAFD089965](https://moksha.vanascan.io/address/0xF084Ca24B4E29Aa843898e0B12c465fAFD089965)  
Moksha: [0x3c92fD91639b41f13338CE62f19131e7d19eaa0D](https://moksha.vanascan.io/address/0x3c92fD91639b41f13338CE62f19131e7d19eaa0D)


### [Root Network Contract](https://docs.vana.org/vana/core-concepts/key-elements/smart-contracts#root-network-contract)

The DLP Root contract manages the registration and reward distribution for Data Liquidity Pools (DLPs) in the Vana ecosystem. It operates on an epoch-based system, where the top 16 most staked DLPs and their stakers receive rewards at the end of each epoch. The contract allows users to stake VANA tokens as guarantors for DLPs, with rewards distributed based on the staking position at the end of each epoch.

Moksha:  [0xff14346dF2B8Fd0c95BF34f1c92e49417b508AD5](https://moksha.vanascan.io/address/0xff14346dF2B8Fd0c95BF34f1c92e49417b508AD5)  


### [Data Liquidity Pool & DLPToken](https://docs.vana.org/vana/welcome-to-vana/what-is-data-liquidity-pool)

A Data Liquidity Pool (DLP) is a core component of the Vana ecosystem, designed to transform raw data into a liquid asset. It functions as a smart contract on the Vana blockchain that allows users to monetize, control, and govern their data in a decentralized manner. Each DLP can have its own token, providing contributors with ongoing rewards and governance rights.

**DataRegistry**, **TEEPool**, and **RootNetwork** are part of the Vana core smart contracts and do not need to be deployed by DLP builders. For testing and integration, you should use the addresses deployed on Moksha. However, you will need to deploy your own **Data Liquidity Pool** & **DLPToken** (either the template version suggested by us or your own version). Keep in mind that to be part of the Vana ecosystem and qualify for the DLP rewards program, the DLP contract needs to be integrated with **DataRegistry** and **RootNetwork** as shown in the template in this repository.


## 3. Flow

### Data Contributor Flow

The following describes the process of contributing data to a DLP from a user's perspective:

Bob wants to become a data contributor for DLP1. Here's the step-by-step process:

1. Bob uploads his file to the DataRegistry (a URL with encrypted data).  
E.g.https://moksha.vanascan.io/tx/0x900e23d55bf7706973376ff7da5a649bbf3d470bfd9020dc66d9830fd4dbd1d3
2. Bob requests an attestation by adding a new job in the TeePool.  
E.g.https://moksha.vanascan.io/tx/0x40c58020c0cf10c8c53e412f209b60c923dc7a8c7513bf94fefe189a736b7f96?tab=logs
3. TEE operators see Bob's job and create an attestation for that file based on the instructions required for validating the file in relation to DLP1.
4. This proof is saved in the DataRegistry.  
E.g.https://moksha.vanascan.io/tx/0x2f4dba67e90685429b73a43e74fe839e580c9e50f60ce5d460b19f88f56a2e99?tab=index
5. Bob must grant access to the DLP to read the data (by encrypting the encryption key of the file with the specific publicKey of DLP1).  
E.g.https://moksha.vanascan.io/tx/0xfeeda337eeb60367a8332a664087cbef5b4e7f0882af30e36c5259c43a7042cc

    This step can be done by Bob in the first step by passing the permission along with the file.  
E.g.https://moksha.vanascan.io/tx/0xb54582c8bfa1940a2003dff4aa729f36effeef4537181e1a68d009b32a5880d0

7. After Bob's file receives the necessary attestation, he must inform DLP1 that he owns a file and a valid proof intended for this DLP. Bob will be automatically rewarded based on the score obtained from the attestation by the TEE operator.  
E.g. https://moksha.vanascan.io/tx/0x69c07a8e0e5fd3a2f9b9c063c4c4f1f56a9aabb18b5c0f07bd10107d0844ebd9  

This process ensures that data is securely contributed, validated, and rewarded within the Vana ecosystem.

To save Bob from paying transaction fees, the DLP can act as a proxy between Bob and the smart contracts. Bob provides the DLP app with the necessary information, and the DLP can perform all the required transactions on Bob's behalf. Bob remains the owner of the file and the recipient of the allocated reward.

It's important to emphasize that this is just an example of Bob's interaction with the smart contracts. In practice, there should be a user interface (UI) that comes packaged with these contracts to assist users. This UI would simplify the process for users, making it easier for them to interact with the DLP ecosystem without needing to directly interact with the smart contracts.

### Reward distribution

The RootNetwork smart contract manages the reward distribution for Data Liquidity Pools (DLPs) in the Vana ecosystem. Here's a detailed explanation of how the reward system works:

#### DLP Registration and Staking

1. Each DLP must register in the RootNetwork contract using the `registerDLP` method.
2. During registration, the DLP specifies a `stakersPercentage`, which determines the proportion of rewards that will go to the DLP's stakers. The remainder goes to the DLP owner.
  
E.g. https://moksha.vanascan.io/tx/0x84532d83be589ec1c13d9de04e426dcc7c54652060f8f78032a416d9f5dc159b

#### Epoch System

- The RootNetwork operates on an epoch-based timeline (21 days on mainnet, 21 hours on moksha)

#### DLP Selection Process

1. At the end of each epoch, the top 16 DLPs are selected based on their total staked amount.  
2. Only these 16 DLPs receive rewards for the epoch that just ended.  
3. Only stakers for these 16 DLPs receive rewards for the epoch that just ended.   

#### Reward Distribution

1. At the end of each epoch, rewards are distributed to the 16 participating DLPs based on the total amount staked for each DLP.
2. For each DLP:
    - A portion of the reward goes to the DLP owner.
    - The rest is reserved for the DLP's stakers (as per the `stakersPercentage`).
3. Staker rewards are not distributed automatically; each staker must claim their rewards individually.

#### Data Contributor Rewards

- Each DLP is responsible for rewarding its data contributors.
- The method and currency for these rewards are at the discretion of each DLP.
- In the DLP template from this repository:
    - The DLP uses its own currency for rewards.
    - Reward amount is proportional to the score of the uploaded file.
    - More details can be found in the specific DLP documentation.

#### Flexibility in Reward Mechanisms

- DLPs can modify their reward mechanism for data contributors:
- They may choose to use VANA tokens instead of a custom currency.
- They could distribute part or all of the rewards received from the RootNetwork to data contributors.
- More complex reward calculation mechanisms can be implemented.

#### Important Considerations

- The selection of top DLPs occurs at the end of each epoch, so staking positions at that time are crucial.
- Stakers should regularly check and claim their rewards to ensure they receive their share.
- DLP owners should carefully consider their `stakersPercentage` to balance attracting stakers and maintaining profitability.
- Data contributors should understand the specific reward mechanism of each DLP they contribute to.

This reward system incentivizes DLPs to perform well and attract stakers, while also providing flexibility in how they reward their data contributors. It creates a competitive ecosystem where DLPs strive for top positions to participate in epochs and earn rewards.


## 4. Installation

#### 1. Environment Setup

Before deploying or interacting with the contracts, you need to set up your environment variables. Follow these steps:
- Copy the `.env.example` file and rename it to `.env`. 
- Open the `.env` file and update the following parameters:

`DEPLOYER_PRIVATE_KEY`: The private key of the account that will deploy the contracts. Make sure to keep this private and never share it. (E.g. **29bb1493d4f3b0042857e4a23452fc610d2c4e42b95ef5c13f8941eedb37e408**)

`OWNER_ADDRESS`: The Ethereum address that will be set as the owner of the deployed contracts. This address will have special privileges in the contracts. (E.g. **0x853407D0C625Ce7E43C0a2596fBc470C3a6f8305**)

<a id="env-truested_forwarder_address"></a>
`TRESTED_FORWARDER_ADDRESS`: The address of the trusted forwarder contract. This contract is used for gasless transactions. (E.g. **0x853407D0C625Ce7E43C0a2596fBc470C3a6f8305**). Read [gelato documentation](https://docs.gelato.network/web3-services/relay/supported-networks#new-deployments-oct-2024) for more details.  
The integration with gelato is optional, you can set this parameter to 0x0000000000000000000000000000000000000000 if you don't want to use it.  
The ERC2771 integration was added in v1 of the Vana Framework. If you've already deployed the [v0 version](https://github.com/vana-com/vana-dlp-smart-contracts/tree/v0), you can still use the Gelato integration by using the [sponsoredCall](https://docs.gelato.network/web3-services/relay/non-erc-2771/sponsoredcall) function.

`DLP_NAME`: The name of your Data Liquidity Pool. Choose a descriptive name for your DLP. (E.g. **CookieDLP**)

<a id="env-dlp-public-key"></a>
`DLP_PUBLIC_KEY`: A public key for your DLP, used for encryption purposes. This key allows data contributors to encrypt the encryption key securely. Your DLP can then use the corresponding private key to decrypt and access the encrypted files. For more details on the encryption process, refer to the [data privacy section](https://docs.vana.org/docs/data-privacy#code-samples).

Generate a Private-Public Key Pair using the [Vana CLI](https://github.com/vana-com/vana-framework?tab=readme-ov-file#wallets) with the following command:

```bash
vanacli wallet create
```

The command will create a wallet in `~/.vana/wallets/` which will look like this:

```json
{
  "address": "0x123...",
  "publicKey": "0xabc...",
  "privateKey": "0x111..."
}
```

1. **Copy the `publicKey` value** and add it to your `.env` file as `DLP_PUBLIC_KEY`.
2. **Securely store the `privateKey` value.** The private key is critical for decrypting files and must be kept in a safe and secure location. Loss of the private key will prevent access to encrypted files.


`DLP_FILE_REWARD_FACTOR`: A factor used to calculate file rewards. This value determines the reward amount based on the file's score and is represented with 18 decimals. (E.g. **2e18** => the reward multiplier is 2)

`DLP_PROOF_INSTRUCTION`: The instruction for generating proofs. This should match the instruction used by the TEE operators for validating proofs. (E.g. **https://github.com/vana-com/vana-satya-proof-template/releases/download/v24/gsc-my-proof-24.tar.gz**)

#### 2. Install dependencies
```bash
yarn install
```

#### 3. Run tests
- DLP tests: ```npx hardhat test test/dlp.ts```
- DLPToken tests: ```npx hardhat test test/token.ts```
- all tests (including dependencies): ```npx hardhat test```

#### 4. Deploy your own Token & DLP
```bash
npx hardhat deploy --network moksha --tags DLPDeploy  
```

The deployment script will also verify the contract on blockscout.

#### 5. Register your DLP on the RootNetwork
After deploying your DLP, you need to register it on the RootNetwork contract. This will allow your DLP to participate in the Vana ecosystem and receive rewards. To register your DLP, call the `registerDlp` function on the RootNetwork contract.

   ```solidity
    struct DlpRegistration {
        address dlpAddress;
        address ownerAddress;
        address payable treasuryAddress;
        uint256 stakersPercentage;
        string name;
        string iconUrl;
        string website;
        string metadata;
    }

    function registerDlp(
        DlpRegistration calldata registrationInfo
    ) external payable
   ```

**Parameters:**
- `params`: A struct containing information about the DLP
    - `dlpAddress`: The address of your DLP contract
    - `dlpOwnerAddress`: The address that will be set as the owner of the DLP and have special privileges to update the DLP parameters. It can be any address (EOA wallet, multisig, DAO contract).
    - `treasuryAddress`: The address where the rewards will be sent. It can be the same as the owner address or a different address (EOA wallet, multisig, DAO contract).
    - `stakersPercentage`: The percentage of rewards that will be distributed to stakers (in 18 decimal format, e.g., 50% would be 50e18). It should be >= minDlpStakersPercentage (>= 50e18 on moksha).
    - `name`: The name of your DLP
    - `iconUrl`: A URL to an icon image for your DLP
    - `website`: The website URL for your DLP
    - `metadata`: Additional metadata for your DLP. This can be any string data you want to associate with your DLP or a link to a JSON file with more detailed information or it can be left empty.
   
- Send the required stake amount with the transaction. The value sent with the transaction (`msg.value`) must be at least the `minDlpStakeAmount` (100 Vana on moksha and mainnet).

E.g.  https://moksha.vanascan.io/tx/0xd3472bfaa68990c2dd7e0c9ff125936a6edcc031be1beb589fc7cc92e683cb46

### After Registration

Upon successful registration:
1. The DLP is assigned a unique ID.
2. The DLP's details are stored in the contract.
3. The sent stake amount is recorded for the DLP owner as a stake.
4. The DLP is added to the list of registered DLPs.
5. Users can start staking VANA tokens to the DLP to participate in the reward distribution.

#### 6. Edit your DLP details

```solidity
function updateDlp(uint256 dlpId, DlpRegistration calldata dlpUpdateInfo) external
```
Updates an existing DLP's information. This method allows modification of various DLP parameters while maintaining its registration status. Note that the DLP address cannot be changed after registration.

**Parameters:**
- `dlpId`: The ID of the DLP to update
- `dlpUpdateInfo`: A struct containing the updated DLP information:
    - `dlpAddress`: The address of the DLP cannot be changed so  it must match the existing address
    - `ownerAddress`: The new owner address for the DLP
    - `treasuryAddress`: The new treasury address for receiving rewards
    - `stakersPercentage`: The new percentage of rewards to be distributed to stakers (in 18 decimal format)
    - `name`: The updated name of the DLP
    - `iconUrl`: The updated URL for the DLP's icon
    - `website`: The updated website URL
    - `metadata`: Updated metadata or additional information

**Restrictions:**
- Can only be called by the current DLP owner
- Contract must not be paused
- New stakersPercentage must be between minDlpStakersPercentage and maxDlpStakersPercentage
- DLP address cannot be changed
- Owner and treasury addresses cannot be zero addresses

**Events Emitted:**
- `DlpUpdated(uint256 indexed dlpId, address indexed dlpAddress, address ownerAddress, address treasuryAddress, uint256 stakersPercentage, string name, string iconUrl, string website, string metadata)`

**Errors:**
- `NotDlpOwner`: Thrown if called by any account other than the DLP owner
- `InvalidAddress`: Thrown if owner or treasury address is zero
- `InvalidStakersPercentage`: Thrown if the new stakersPercentage is outside allowed range
- `DLpAddressCannotBeChanged`: Thrown if attempting to change the DLP address
- `EnforcedPause`: Thrown if the contract is paused

#### 7. Deregister your DLP

```solidity
function deregisterDlp(uint256 dlpId) external
```
Deregisters a DLP from the network, removing it from the list of eligible DLPs. Once deregistered, a DLP can no longer participate in epochs or receive rewards.

**Parameters:**
- `dlpId`: The ID of the DLP to deregister

**Restrictions:**
- Can only be called by the DLP owner

**Events Emitted:**
- `DlpDeregistered(uint256 indexed dlpId)`

**Errors:**
- `NotDlpOwner`: Thrown if called by any account other than the DLP owner
- `InvalidDlpStatus`: Thrown if the DLP is not in a valid status for deregistration
- `EnforcedPause`: Thrown if the contract is paused

After deregistration all stakes associated to that dlp must be closed by the stakers.  
DLP owner must also close the initial stake used to register the DLP.

## 5. Dlp Contracts

### DataLiquidityPool

This contract is designed to be upgradeable using the Universal Upgradeable Proxy Standard (UUPS) pattern. This allows for future improvements while maintaining the contract's state and address.

For more information on the UUPS pattern and how to work with upgradeable contracts, please refer to the OpenZeppelin documentation:
- [Proxy Upgrade Pattern](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies)
- [Writing Upgradeable Contracts](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable)

#### Methods

```solidity
function initialize(InitParams memory params) external initializer
```
Initializes the contract with the given parameters.

**Parameters:**
- `params`: A struct containing initialization parameters
    - `truestedForwarder`: The address of the trusted forwarder contract. See [this section](#env-truested_forwarder_address) for more details.
    - `ownerAddress`: The address of the contract owner. (E.g. **0x853407D0C625Ce7E43C0a2596fBc470C3a6f8305**)
    - `tokenAddress`: The address of the ERC20 token used for rewards. (E.g. **0xF3D9A139a7ba707843dD4f1FDfE0F9E55D9D8d6b**)
    - `dataRegistryAddress`: The address of the data registry contract. (E.g. **0xEA882bb75C54DE9A08bC46b46c396727B4BFe9a5**)
    - `teePoolAddress`: The address of the TEE pool contract. (E.g. **0xF084Ca24B4E29Aa843898e0B12c465fAFD089965**)
    - `name`: The name of the data liquidity pool. (E.g. **CookieDLP**)
    - `publicKey`: A public key for your DLP, used for encryption purposes. See [this section](#env-dlp-public-key) for more details.
    - `proofInstruction`: The instruction for generating proofs. (E.g. **https://github.com/vana-com/vana-satya-proof-template/releases/download/v24/gsc-my-proof-24.tar.gz**)
    - `fileRewardFactor`: The factor used to calculate file rewards. (E.g. **2e18** => the reward multiplier is 2)


Tee validators calculate a total score for the files they verify, based on the set of instructions executed to generate the proof. This score ranges between 0 and 1 (i.e., between 0 and 1e18). At the end, the dataContributor will receive a reward. DLP_FILE_REWARD_FACTOR is used to convert the score into the amount of DLP tokens the data contributor receives for their file. Essentially, the reward is calculated using the formula:  
```reward = fileScore * DLP_FILE_REWARD_FACTOR ```  
  
**Restrictions:** Can only be called once during contract deployment

**Events Emitted:** None

**Errors:**
- `InvalidInitialization`: Thrown if the contract has already been initialized

---

```solidity
function version() external pure returns (uint256)
```
Returns the version of the contract.

**Returns:** The version number

**Restrictions:** None

---

```solidity
function files(uint256 fileId) public view returns (FileResponse memory)
```
Retrieves information about a specific file.

**Parameters:**
- `fileId`: The ID of the file

**Returns:** `FileResponse` struct containing:
- `fileId`: The ID of the file
- `timestamp`: The timestamp when the file was added
- `proofIndex`: The index of the proof associated with the file
- `rewardAmount`: The amount of reward for the file

**Restrictions:** None

---

```solidity
function filesListCount() external view returns (uint256)
```
Returns the total number of files in the pool.

**Returns:** The number of files

**Restrictions:** None

---

```solidity
function filesListAt(uint256 index) external view returns (uint256)
```
Retrieves the file ID at a specific index in the files list.

**Parameters:**
- `index`: The index in the files list

**Returns:** The file ID at the given index

**Restrictions:** None

---

```solidity
function contributors(uint256 index) external view returns (ContributorInfoResponse memory)
```
Retrieves information about a contributor at a specific index.

**Parameters:**
- `index`: The index of the contributor

**Returns:** `ContributorInfoResponse` struct containing:
- `contributorAddress`: The address of the contributor
- `filesListCount`: The number of files contributed by this contributor

**Restrictions:** None

---

```solidity
function contributorInfo(address contributorAddress) public view returns (ContributorInfoResponse memory)
```
Retrieves information about a specific contributor.

**Parameters:**
- `contributorAddress`: The address of the contributor

**Returns:** `ContributorInfoResponse` struct (same as `contributors` method)

**Restrictions:** None

---

```solidity
function contributorFiles(address contributorAddress, uint256 index) external view returns (FileResponse memory)
```
Retrieves information about a specific file contributed by a contributor.

**Parameters:**
- `contributorAddress`: The address of the contributor
- `index`: The index of the file in the contributor's files list

**Returns:** `FileResponse` struct (same as `files` method)

**Restrictions:** None

---

```solidity
function pause() external
```
Pauses the contract, preventing certain operations.

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** None (inherited from PausableUpgradeable)

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function unpause() external
```
Unpauses the contract, re-enabling paused operations.

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** None (inherited from PausableUpgradeable)

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function updateFileRewardFactor(uint256 newFileRewardFactor) external
```
Updates the file reward factor used to calculate rewards.

**Parameters:**
- `newFileRewardFactor`: The new file reward factor

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** `FileRewardFactorUpdated`

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function updateTeePool(address newTeePool) external
```
Updates the address of the TEE pool contract.

**Parameters:**
- `newTeePool`: The new TEE pool contract address

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** None

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function updateProofInstruction(string calldata newProofInstruction) external
```
Updates the proof instruction used for validating proofs.

**Parameters:**
- `newProofInstruction`: The new proof instruction.

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** `ProofInstructionUpdated`

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function updatePublicKey(string calldata newPublicKey) external
```
Updates the public key of the pool.

**Parameters:**
- `newPublicKey`: The new public key (E.g. **0x04bfcab8282071e4c17b3ae235928ec9dd9fb8e2b2f981c56c4a5215c9e7a1fcf1a84924476b8b56f17f719d3d3b729688bb7c39a60b00414d53ae8491df5791fa**)

**Restrictions:** Can only be called by the contract owner

**Events Emitted:** `PublicKeyUpdated`

**Errors:**
- `OwnableUnauthorizedAccount`: Thrown if called by any account other than the owner

---

```solidity
function requestReward(uint256 fileId, uint256 proofIndex) external
```
Requests a reward for a file based on its proof.

**Parameters:**
- `fileId`: The ID of the file from the data registry
- `proofIndex`: The index of the proof for the file

**Restrictions:**
- Contract must not be paused
- File must not have been already rewarded
- Proof must be valid and signed by a registered TEE

**Events Emitted:** `RewardRequested`

**Errors:**
- `EnforcedPause`: Thrown if the contract is paused
- `FileAlreadyAdded`: Thrown if the file has already been rewarded
- `InvalidProof`: Thrown if the proof instruction doesn't match the contract's proof instruction
- `InvalidAttestator`: Thrown if the proof is not signed by a registered TEE

---

```solidity
function addRewardsForContributors(uint256 contributorsRewardAmount) external
```
Adds rewards to the pool for contributors.

**Parameters:**
- `contributorsRewardAmount`: The amount of rewards to add

**Restrictions:** None

**Events Emitted:** None

**Errors:**
- `ERC20InsufficientAllowance`: Thrown if the caller has not approved enough tokens for transfer
- `ERC20InsufficientBalance`: Thrown if the caller does not have enough tokens to transfer


### 
###

---

### DAT (Data Access Token)


The DAT contract is an ERC20 token with additional governance features, including ERC20Permit for gasless approvals and ERC20Votes for on-chain voting capabilities. It also includes custom functionality for minting control and address blocking.

This contract inherits from OpenZeppelin's ERC20, ERC20Permit, ERC20Votes, and Ownable2Step contracts, providing standard ERC20 functionality along with permit capabilities for gasless approvals, voting mechanisms, and secure ownership management.

###
```solidity
constructor(string memory name, string memory symbol, address ownerAddress)
```

Description: Initializes the contract by setting a name, symbol, and owner address.

Parameters:
- `name`: Name of the token
- `symbol`: Symbol of the token
- `ownerAddress`: Address of the initial owner

Restrictions: None

Events Emitted: None (inherited from ERC20 and Ownable)

###
```solidity
function clock() public view override returns (uint48)
```

Description: Returns the current timestamp, used for ERC20Votes functionality.

Parameters: None

Return Value:
- `uint48`: Current block timestamp

Restrictions: None

Events Emitted: None

###
```solidity
function CLOCK_MODE() public pure override returns (string memory)
```

Description: Returns the mode of the clock (timestamp-based).

Parameters: None

Return Value:
- `string`: "mode=timestamp"

Restrictions: None

Events Emitted: None

###
```solidity
function blockListLength() external view returns (uint256)
```

Description: Returns the number of addresses in the block list.

Parameters: None

Return Value:
- `uint256`: Length of the block list

Restrictions: None

Events Emitted: None

###
```solidity
function blockListAt(uint256 _index) external view returns (address)
```

Description: Returns the address at a given index in the block list.

Parameters:
- `_index`: Index in the block list

Return Value:
- `address`: Address at the specified index

Restrictions: None

Events Emitted: None

###
```solidity
function nonces(address owner) public view virtual override(ERC20Permit, Nonces) returns (uint256)
```

Description: Returns the current nonce for an address, used for ERC20Permit functionality.

Parameters:
- `owner`: Address to check the nonce for

Return Value:
- `uint256`: Current nonce for the address

Restrictions: None

Events Emitted: None

###
```solidity
function mint(address to, uint256 amount) external virtual onlyOwner whenMintIsAllowed
```

Description: Mints new tokens to a specified address.

Parameters:
- `to`: Address to mint tokens to
- `amount`: Amount of tokens to mint

Restrictions:
- Only owner can call
- Minting must not be blocked

Events Emitted: Transfer event (inherited from ERC20)

###
```solidity
function changeAdmin(address newAdmin) external virtual onlyOwner
```

Description: Changes the admin address.

Parameters:
- `newAdmin`: New admin address

Restrictions: Only owner can call

Events Emitted:
- `AdminChanged(address indexed oldAdmin, address indexed newAdmin)`

###
```solidity
function blockMint() external virtual onlyOwner whenMintIsAllowed
```

Description: Blocks further minting of tokens permanently.

Parameters: None

Restrictions:
- Only owner can call
- Minting must not already be blocked

Events Emitted:
- `MintBlocked()`

###
```solidity
function blockAddress(address addressToBeBlocked) external virtual onlyAdmin
```

Description: Adds an address to the block list, preventing it from transferring tokens.

Parameters:
- `addressToBeBlocked`: Address to be blocked

Restrictions: Only admin can call

Events Emitted:
- `AddressBlocked(address indexed blockedAddress)`

###
```solidity
function unblockAddress(address addressToBeUnblocked) external virtual onlyAdmin
```

Description: Removes an address from the block list.

Parameters:
- `addressToBeUnblocked`: Address to be unblocked

Restrictions: Only admin can call

Events Emitted:
- `AddressUnblocked(address indexed unblockedAddress)`
