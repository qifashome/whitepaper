- [Background](#background)
  - [Smart Contract and Decentralized Application Platforms](#smart-contract-and-decentralized-application-platforms)
  - [A Changing External Environment](#a-changing-external-environment)
  - [The Conundrum of DeFi](#the-conundrum-of-defi)
  - [Oracles](#oracles)
  - [Evolution of Performance](#evolution-of-performance)
- [Introduction](#introduction)
- [Microkernel](#microkernel)
  - [Component Simplicity](#component-simplicity)
  - [Denial of Service Attack](#denial-of-service-attack)
  - [Endpoint-based Security Mechanism](#endpoint-based-security-mechanism)
- [Plugin Mechanism](#plugin-mechanism)
  - [Plugin Registration](#plugin-registration)
  - [Plugin Call](#plugin-call)
  - [Remove Plugin](#remove-plugin)
  - [Upgrade Plugin](#upgrade-plugin)
  - [Event Interception](#event-interception)
  - [Activation Moment](#activation-moment)
  - [Additional Configurations](#additional-configurations)
  - [Dependencies and Communication Between Plugins](#dependencies-and-communication-between-plugins)
  - [Reflections](#reflections)
- [Consensus](#consensus)
  - [Submit a New Transaction](#submit-a-new-transaction)
  - [Validity Check](#validity-check)
  - [Consensus](#consensus-1)
  - [Feasibility](#feasibility)
  - [Double Spending](#double-spending)
- [System Composition](#system-composition)
  - [Content Server](#content-server)
  - [Qifas Decentralized Identifier(QDID)](#qifas-decentralized-identifierqdid)
  - [Account](#account)
  - [Address](#address)
  - [Currency Symbol](#currency-symbol)
  - [Master Key Pair](#master-key-pair)
  - [Regular Key Pair](#regular-key-pair)
  - [Multi-Signing](#multi-signing)
- [Management](#management)
  - [Transfer Authorization](#transfer-authorization)
  - [Disable Token](#disable-token)
  - [Global Freeze](#global-freeze)
- [Applications](#applications)
  - [Decentralized Identifier](#decentralized-identifier)
  - [Encrypted Communication](#encrypted-communication)
  - [Decentralized Storage](#decentralized-storage)
  - [Decentralized Exchange](#decentralized-exchange)
- [Citations](#citations)




# Background	

## Smart Contract and Decentralized Application Platforms

Smart contracts are a set of "if/when...then..." statements on the blockchain that accomplish a specified task, however the limited functionality of the virtual machines restricted the smart contract ability, which makes the smart contracts neither smart nor contracts. There are many problems in building in-blockchain loans, bonds, and derivatives based on smart contract. Consider a debt contract: first transfer X number of Tokens from address A to address B, and after some time transfer Y number of Tokens from address B to address A (generally Y>X). In the real environment, such contracts often involve a complex payoff structure, and the contract-triggering conditions usually depend on information outside the blockchain, which needs to be written into the blockchain first, it is difficult to map real world to the blockchain. Two types of oracle machines have been discussed to address this issue, one is to rely on a centralized information source, but this runs counter to the decentralized purpose of the blockchain. The second is to disaggregate information outside the blockchain and then write it into the blockchain using financial incentives and voting. Such mechanisms rely on group wisdom to reward and punish voters based on voting results, the closer the vote is to the mean, median, or other sample statistics of all votes, the more likely the voter will be rewarded, and vice versa, as an incentive for voters to vote carefully, this implies that there is no systematic bias in voting by the group of participants. However, this assumption does not necessarily hold in reality, and decentralized oracle machine solution is not perfect so far. In addition, current smart contract languages generally lack standard libraries, contract code does not support upgrades only deployments, and storage space is fragmented.

![](https://github.com/qifashome/whitepaper/blob/main/images/Figure1.png?raw=true)

Figure 1 summarizes the structure of the blockchain, in which the ledger, smart contract, and consensus algorithm are all within the consensus boundary, the ledger and smart contract are inextricably linked to each other, the consensus algorithm ensures a trustless environment within the consensus boundary. The information in the blockchain that is not related to the state of the token is outside the consensus boundary and inside the blockchain boundary.

## A Changing External Environment

Blockchain is a deterministic environment which does not allow for uncertainties. In general, the execution of smart contracts requires trigger conditions, and when the trigger condition of a smart contract is external information, in order to ensure that the smart contract get a consistent result no matter when and where it runs, it does not support external requests, that is, the virtual machine (VM) cannot let the smart contract have network call, otherwise, the result is uncertain. Smart contracts can't do I/O, Actually smart contract is doing a less intelligent thing, it is triggered by predefined conditions, and in some cases, it may also requires the contract-related people to sign the private key before it can be executed.

## The Conundrum of DeFi

Suppose there is a collateral that needs to be triggered to close when the market price falls below a specific value, at which point the system needs continuously obtain the real-time price of market. If decentralized nodes all over the network acquire price from the exchange or market at the same time, while the price in the market is changing simultaneously, plus network latency, computational speed, etc., the price obtained by each node may not be the same, if this data is input to the smart contract, which will cause the nodes cannot reach consensus, then the whole system will crash.

## Oracles

Oracles are data feeds that information outside the blockchain is written into the blockchain. It empowers smart contracts to interact with the external world. There are two kinds of Oracles, centralized and decentralized, the current centralized Oracles' gas fee is high and can only be used for individual networks. Decentralized solutions such as using smart contracts and off-chain data nodes to request and feed data through reward-punishment mechanisms and aggregation models, also have problems such as higher cost of chain aggregation, poor scalability, and centralized risk of reputation-based systems.

## Evolution of Performance

There are many discussions on how to improve blockchain performance, including trunking networks, sharding, increasing block sizes, segregated witness(SegWit), directed acyclic graph structure (DAG), cross-chain, sidechain, state channels (represented by the Bitcoin Lightning Network), and techniques to compress transaction information (e.g. Mimblewimble), etc. Another important direction to improve the efficiency of blockchains is to optimize the consensus algorithm, especially by moving from proof of work (POW) to proof of stake (POS). Using federated or private chains instead of public chains in some application scenarios is also an important direction. Performance may be just one reason; the lack of a comprehensive solution is also a signiﬁcant impediment to blockchain usage.

# Introduction

Qifas is a development platform for decentralized applications. External applications connecting to the blockchain require a comprehensive development platform that can integrate technologies such as decentralized identifiers, on-chain storage management, decentralized computing, security and privacy protection, and plugin mechanism, with which to cope with various complex scenarios and provide abstract design capabilities. Qifas is designed for this purpose that draws on existing technologies, and compliances with international standard network protocols. it seeks to create a simple platform for connecting to the outside world. Through a combination of several technologies to break through the bottleneck of blockchain and establish a perpetual open source platform.

# Microkernel

The system is divided into kernel state and user state. Service components that require special privileges are located in the kernel state, and services that need frequent changes are placed in the user state while allowing them to be started or stopped individually. Adding arbitration services to the microkernel, building user-state protocol stacks, and using kernel-state interrupts thread. This design allows for better security due to effective system isolation, and arbitration services make applications run lock-free on multi-core platforms. Two types of communication are available in the microkernel: event notification and message delivery. Theoretically, the microkernel only needs to provide two system calls, send/receive.

![](https://github.com/qifashome/whitepaper/blob/main/images/Figure2.png?raw=true)

## Component Simplicity

The microkernel provides the basic functionality and primitives, and we can build any application based on it. it provides some core functionality such as:

* signature,cryptographic,hash algorithm
*  consensus mechanism
* blockchain I/O
* content server read/write
* scheduler,management timer

The microkernel uses a layered design, it provides services such as signature, cryptographic, hash algorithm, consensus mechanism, and chain read/write. the middle layer provides smart contracts, unified network services, and external information access services, it runs in the user state, and the top layer is the user applications. The smart contract runs as a user program in the user state, and user mode programs communicate with each other through the channel communication mechanism.

## Denial of Service Attack

Consider an attacker who bypasses system services and directly requests core services that the attacker does not have permission to access, leaving the system fully loaded for a long time. The system uses a capability space to prevent denial of service attacks, as detailed below.

## Endpoint-based Security Mechanism

The system uses capacity space to prevent service from abuse. Capability space is a token that cannot be forged. When a process has a token, it is given a capability, such as the ability to access the network, which can be copied, transferred, or transmitted via IPC. Only kernel state services can control and assign tokens, thus ensuring that user-state applications cannot abuse system services by bypassing the capability space mechanism. In addition, the system is equipped with fault isolation and automatic restart of failed services.

Only basic services are placed in the kernel state, which facilitates the formal proof of the microkernel's correctness, with the help of mathematical software to automatically derive and check every operational state of the system to prove the security of the microkernel. It is possible to determine precisely how the kernel behaves in all cases without crashing or running insecure operations. And It is found that frequently used attack methods, such as cache overflow vulnerabilities often used by malicious programs, buffer overflows, and null pointer exceptions can be effectively avoided.

# Plugin Mechanism

Qifas enriches the system functionality through plugins, which are used as a bridge to connect the external applications. Many development frameworks have plugin mechanisms, such as grunt, cordova, gulp, webpack, egg, koa, plugin mechanism is a common choice for platforms to improve their scalability and maintainability. As a decentralized application development platform, Qifas uses plugins to expand its ability too, the more plugins are available, the less development work is required, thus reducing development costs.

The plugin mechanism has the following features：

* A public library of plugins.
* Sandboxing mechanism to isolate the core code, even if there are problems with the plugin code, it will not affect the stability of the core code.
* Support continuous upgrades to meet the specific needs of different business scenarios.
* A categorized index database, which is especially important in scenarios with a large amount of code.
* Slotted plugins that can be loaded and unloaded easily.
* Basic support features are available in the form of plugins for the entire network to use.

## Plugin Registration

```
Qifas plugin add [<plug-in-folder>,<network-location>] <group-id> <artifact-id> <version>
```

`<plug-in-folder>,<network-location>` : The path used to locate the Qifas plugin, e.g. c:\qifas_repositories\NetInfoPlugin, or a location `<network-location>` in the network library.

`<group-id>`: The package path id to which the plugin belongs, is defined in segments: [Domain. Company name . Project package name]. 

`<artifact-id>`: The custom plugin id in the project, together with the package path id, uniquely identify a component.

`<version>`:  The version number of a plugin. For read-only plugins that cannot be modified after release, this version number is not necessary.

## Plugin Call

```
plugin.network.get(a...)
```

## Remove Plugin

```
Qifas plugin remove Info
```

## Upgrade Plugin

Upgrading a plugin is divided into two steps: removing and reinstalling:

Remove Plugin

```sh
Qifas plugin remove Info 
```

1. Unpack the new plugin
2. Install the new plugin

```
Qifas plugin add <plug-in_folder>
```

## Event Interception

The system triggers the execution of the plug-in through events and callback functions, for example, by having the plug-in intercept the event `something happen`, to form a program execution lifecycle process.

```
context.on("something happen", do something);
```

## Activation Moment

Some examples of activation are as follows:

- onLanguage: Files containing a certain type of language are opened.
- onCommand: A command call
- onDebug: Start debugging
- onDebugInitialConfigurations
- onDebugResolve
- workspaceContains: The file matching the rule is opened
- onFileSystem: Open a file with a special protocol
- onView: The view is displayed
- onUri: The schema registered with the operating system
- onWebviewPanel: When the webview of a viewType is opened

## Additional Configurations

- contributes.configuration: user-configurable options available for this plugin
- contributes.configurationDefaults: default configurations
- contributes.commands: Register some commands with Qifas's command system that can be invoked by the user

## Dependencies and Communication Between Plugins

There are currently two ways to handle dependencies between plugins: dependencies are configured in the core system, and dependencies are defined in the plugins. In the form of a configuration file, e.g.

```
{
  "contract-trigger": ["netinfo-loader", "trans-currency-loader"]
}
```

The execution logic is  `netinfo-loader -> trans-currency-loader`; this rule is executed by the core system according to the logic of the configuration file.

Asynchronous hooks include parallel asynchronous hooks and serial hooks, which use the event mechanism to determine the timing of the invocation, while context is mainly for passing data information, context generally has a scope and is strictly related to the execution order. And it works depending on the order of plugins loading, as mentioned above.

Here's how to use a sync hook, divided into three steps: defining the hook, registering the plugin, and executing the hook, with the following pseudo-code demonstration.

```
import { SyncHook } from 'Qifashook';

// Defining a hook
const hook = new SyncHook(['arg']);

// Registering plugin on a hook
hook.tap('preFlightCheckHook', (arg) => {
    console.log('preFlightCheckHook', arg);
});
hook.tap('acceptHandlerHook', (arg) => {
    console.log('acceptHandlerHook', arg);
});

// Execute a plugin function
hook.call('something');

// The console prints the following results
// preFlightCheckHook something
// acceptHandlerHook something 
```

We see the hook in the example as a part of the overall event, and the methods in the tap function are the things to do at that part. We can add multiple callbacks through the tap method, which means that we can do multiple things. We can also set the priority, that is, the order in which things are executed. Finally, the whole process is triggered by the call method.

## Reflections

Plugins are interdependent or fully orthogonal: for example, a smart contract plugin A, depends on plugins B and C, while B and C may be fully orthogonal to each other, or there may be dependencies, in this condition, at that point, it is necessary to distinguish the type of dependency.

Plugin upgrade and feature expansion: When upgrading the original plugin or creating new plugins, if there are already some plugins that can be referenced, needs the system to support this reference, and the rules should be simple and reasonable.

# Consensus

Qifas uses a timing-based POS algorithm with a two-stage consensus. The first stage is packaging the set of legitimate transactions, and the second stage is proposing and consensus on the newly generated set.

## Submit a New Transaction

Every account has a sequence number, which is used to keep the order of transactions. The transaction's LastTxSequence is determined by the last verified ledger sequence number, in some situations, such as several people sharing an account, transactions that require multiple signatures, or signed transactions that are postponed to execution, where the sequence number is temporarily undetermined, we can use a transaction ticket as a replacement, which is a reserved sequence number. This ensures the sequence of transactions and allows for secure re-submission of transactions. The submission also includes data encoding merging, data encryption, and authorization of transactions using account private keys. If it is a multi-signature transaction, the authorization of all accounts needs to be collected.

## Validity Check

The client starts the process by broadcasting a transaction to the network, which is received by two kinds of nodes: validator and tracker. These transactions then flood the entire network by being transmitted to the peer nodes; soon every connected node received them, with the local book data legitimacy verification, comparing logs from the sequence *t-1* and ensuring that changes to the account had been included, checking whether there was enough money to spend, verifying the signature, confirming whether the transaction was issued by the account owner. And if using non-local assets, the verifier ensures that a trusted network exists between the counterparties. After the above checks, illegitimate transactions are discarded, and legitimate transactions, along with those left over from the previous consensus process that could not be confirmed, are aggregated into a transaction candidate set. When the timer runs out, our votes for the current candidate set are sent to other validation nodes as proposals.

## Consensus

The validator continuously analyzes incoming transactions and appends them to the candidate set; at the end of each stage, the candidate set is packed into a single unit, the transactions are hashed into a tree, and the corresponding Merkle roots are signed. The current block number, consensus transaction set's Merkle tree root hash, parent block hash, and current timestamp are put together to compute a block hash, and then the verifier broadcasts its block hash to the visible peers. The verifying node receives the block hash and compares it with its own. If it is the same, the block it packed is confirmed to be a new consensus-packed block and is directly stored locally. If the threshold value is not exceeded, the node continues to exchange proposal packets. After parsing a received transaction, if there is an identical transaction, that transaction gains one vote. When a transaction receives more than 50% of the votes within a certain period, the transaction moves to the next round. Transactions that do not exceed 50% will be left for the next consensus process to confirm. Nodes get all the transactions that have been voted more than 50% in their proposal. In this process, if their hash is different from the consensus hash, they need to go to other nodes to ask for the new block information, store it locally and update the current state. The node repackages the transactions with more than 50% of the votes into a new proposal and sends it to the other nodes, while raising the threshold of required votes to 60% for a second iteration, repeating the process until the threshold reaches 80%. This cycle of new proposals continues in 10% increments until the PPL network reaches a quorum. Once this interval is reached, the node identifies the version hash as the last closed ledger, i.e., the last (latest) state of the ledger, and starts attaching a new ledger version n+1. The algorithm accepts client and peer transactions on the established connection, checks the ledger's sequence number and parent close time against the clock and the last verified ledger, relies on a heartbeat timer to drive the checking process, and applies the held transactions to the open ledger.

## Feasibility

**Fraud**
Qifas randomly selects a subset of validator nodes for each round of block validation; the number of nodes per round of validation is currently set at 30. To reach consensus requires a threshold of 80%, that is as long as the peer node list PPL has 80% of honest nodes, it can reach consensus, and in each round of participation, the verification node is a random subset of the verification node-set, and each round of nodes involved in verification, is a random subset of the set of verification nodes, to reach fraudulent behavior, fraudulent nodes need to hit a round of consensus 80% of the verification nodes, this will pay the price of exponential growth, and no matter fraudulent node or honest node, it can't pass consensus without reaching 80%.

**Convergence of the Algorithm**

For the consensus process, participants can theoretically change the decision to agree on the set of transactions so that the transactions do not reach consensus, and the probability of such failure is very low but cannot be reduced to zero. In the consensus process, each participant submits their respective agreed transaction set and collects the hash vote of peer nodes, at which, if the validators establish the same result set as the absolute majority, the result set can be trusted. If the validator node establishes a result set different from the one reached by the absolute majority, a normally behaving validator, in this case, must establish and accept the ledger agreed by the absolute majority. These are what happens in the vast majority of cases, and in rare cases, no clear absolute majority occurs in the received validation. In this case, it is necessary to discard the previous unanimous consensus and initiate a new round of consistency negotiation in which the inconsistent transactions are removed, which manifests itself as a delay of a few seconds in the network's block time. Suppose a validator set performs a cheating operation and forks out of the network, then when the forked cheating blockchain is connected to the network again, the validators on the Qifas network will find that the block is inconsistent with their hash value, time series, and sequence number, etc., and will be actively disconnected as an invalid node. The blockchain generates a block every 3 seconds or so, with a tps of 2000.

## Double Spending

Every transaction is locally verified first before submitting to the blockchain, and the consensus process may take 3-8 seconds. Before the consensus is confirmed, another transaction can be submitted with this money again, and the local consensus can still be verified as the previous one has not been confirmed; The Qifas handles this situation in such a way that for the validator set of this round, if the consensus of the first transaction is confirmed, the consensus of the second transaction will not pass, and at the same time because the validation nodes of each round are a finite set with a fixed number of validators, which also reduces the possibility of forking.

# System Composition

## Content Server

The content server is a kind of Qifas network node, a decentralized data server, which guarantees that the service provider is responsible for the reliability of the server by mortgaging QCT, The service provider charges QCT as a service fee based on the amount of resources rent. When a content server comes online, it broadcasts a notification message to the network, and soon all nodes in the network will receive and record it in their server list and assign storage tasks to them based on their healthiness.

**Reasons for running content servers**

It is necessary to keep a copy of data per blockchain node to protect assets. But non-ledger data comes from a variety of applications of different entities, and may have a huge amount of data, it is not practical to synchronize all nodes with one copy. And this kind of data often has special data format requirements or needs to be stored on-chain to facilitate network-wide synchronization and play the role of linking individual applications and the blockchain network.

**When content servers are needed**

* The external data needs to be passed into the blockchain for network-wide access. For example, to get the price of assets from the network, when the price meets the conditions, it triggers the execution of a smart contract. because of the different network latency of end users, the prices obtained by terminals may be different, which can cause the smart contract to crash. We can use content servers to solve this problem, the price is submitted to the content server in the form of non-transactional transaction, which is responsible for the synchronization of data across the network, and smart contracts get prices from content servers to ensure consistent price.

* Decentralized Identity. Decentralized identity data is stored in the user's terminal device, such as a cell phone, computer, etc. When the user needs to present his identity, the user decides which data can be provided to the demander publicly. However, the issuance, revocation, and extraction of the user's digital licenses require authorization, including the resolution of DID, which require decentralized storage for the public key, DID, signature, and verification information, to protect against the control of personal information by individual organizations, and to achieve consistent access across the network.

## Qifas Decentralized Identifier(QDID)

QDID is a unique identifier within the Qifas system, it can be used on the plugin, content server, or service running on the content server. QDID is a globally unique identifier that complies with the W3C protocol, it is designed to enable individuals and organizations connecting to Qifas systems with their own generated identifiers, and can prove ownership through signature algorithms. Because each entity controls the generation of its identifiers, it can have as many QDIDs as they need to maintain its required separation of identities, and the use of these identifiers can be appropriately limited to different contexts, with the help of Qifas to enable interactions with other people, institutions, or systems, those interactions usually require entities to identify themselves or the things they control, and also control how much personal or private data should be disclosed, all without relying on a central authority.

QDID is a [RFC3986] compliant URI scheme. The ABNF definition can be found below, which uses the syntax in [RFC5234] and the corresponding definitions for ALPHA and DIGIT. All other rule names not defined in the ABNF below are defined in [RFC3986]. All QDIDs conform to the DID Syntax ABNF Rules.

The QDID Syntax ABNF Rules：

```
did                = did : method-name : method-specific-id
method-name        = 1*method-char
method-char        = %x61-7A / DIGIT
method-specific-id = *( *idchar : ) 1*idchar
idchar             = ALPHA / DIGIT / "." / "-" / "_" / pct-encoded
pct-encoded        = "%" HEXDIG HEXDIG
did url            = did : method-name : method-specific-id/path/to/resource
```

The QDID method specifies the exact operations for creating, resolving, updating, and deactivating QDID. it is often associated with a particular verifiable data registry. This is a generic interface, and the specific implementation can be chosen by the entity itself. Any institution, organization, or individual can use this standard to control their QDID generation and control the disclosure of private information, verify QDID attribution through cryptographic proofs during interaction with other entities. However, the following forms of attack on QDID operations must be fully considered: eavesdropping, replay, message insertion, deletion, modification, denial of service, amplification, and man-in-the-middle. 

## Account

An account on the Qifas network includes an identifying base58 encoded address for receiving payments such as qQNxeq9e1ZLwe1wc4ouKCD6nLp41R9wMVZ, an outstanding balance, activation information, and a sequence number. When an account is activated, the sequence starts at 1 and tracks each subsequent transaction. The account also includes a master key that is generated along with the address, which can be disabled and replaced with a regular key, and the regular key can be changed at any time to protect the account. The account supports configuration model that changes the consensus through configuration. There is also account-related information, including the trust list of other assets owned by the account.

## Address

The address is a base58 character string that identifies the account, it is derived from the master public key, and starts with "q". The address is an alphanumeric character string of 26-34 characters in length, excluding the confusing number 0, the capital letter I, the capital letter O, and the lowercase  letter i, and is case-sensitive. The generation of the address is a cryptographic calculation process, which can be performed offline, and the address is derived from the public key in one way only; that is, the public key can generate the address, but the address cannot infer the public key. 
The process of generating the address is, generate 32 bytes of the private key from a random seed with higher security, generate 33 bytes of the public key from the private key, generate the account id from the public key, and then get the address from the account id using base58, the whole generating process is based on secp256k1 and Ed25519.
Transfers between addresses can be tagged with a destination number. When an address receives transfers from multiple parties at the same time, they can tell the senders to attach a tag number along with the transfer transaction, which can effectively distinguish the source of the transfer and avoid creating multiple addresses.

## Currency Symbol

The currency is represented as a 3-character currency code or a 40-character uppercase hexadecimal string. We recommend using only the uppercase ISO 4217 currency code. The following characters can be used: all uppercase and lowercase letters, numbers, and symbols. , ! , @, #, $, %, ^, &, *, <, >, (,), {, }, [,], and |.

## Master Key Pair

The secret key pair that generates the account is called the master secret key pair, the address is derived from the master secret key pair. Master secret key pairs are better stored offline, for example, written on a piece of paper, placed in a safe place, and used only in emergencies, for example, to change a regular key pair in the event of a compromise. The master key pair has the highest privileges and can perform the following operations in addition to normal operations:

* Disable the master key pair.
* Only the master secret key can permanently disable the freeze ability for the token issued by that address.
* The master key can be disabled, and when the master key is leaked, we can disable the master key and use the regular secret key instead.

## Regular Key Pair

To protect the security of the account, we can set the regular secret key after the account is created and change the regular secret key periodically. The transaction of replacing the regular secret key can be executed using the previous regular secret key. The regular secret key can be used for transactions like transferring funds, multi-signing, and enabling the master key. If the regular private key is destroyed, you can simply delete or change the regular private key pair to regain control of your account.


## Multi-Signing

Multi-signing means that for a specified address, you need to use multiple addresses' private keys for signature to complete a transaction (e.g. a transfer), this usually happens when agreement from multiple parties is required to transfer assets out of the address. Multiple signatures can be pre-set with a quorum and a Signer Weight for each person, and the transaction can be completed when the quorum multiplied by the weight reaches a specified threshold.

Taking the transfer process as an example, the multi-signing address sends a binding transaction, binds multiple signature addresses, and after successful binding, if you want to transfer assets from the multi-signing address, the multi-signing address needs to initiate a transfer transaction, and the transaction information only has the necessary fields such as payee, amount, etc., the multi-signing address can send the transaction with the basic elements of the transfer to the signer A, A checks the transaction's correctness, signs the transaction and sends the signed transaction to another signer B. Keep doing this until the signature threshold is reached and the transaction is completed. In this process, there is no difference in the order of the signers. It is also possible for the owner of multi-signing addresses to collect signatures in parallel, and after the signatures are gathered, a total signature transaction is formed and submitted to complete the transfer at one time.
Accounts participating in multi-signing can be inactive; that is, they can be zero-asset accounts.

# Management

## Transfer Authorization

The account can prevent the deposit of non-consensual assets, which means to initiate a transfer, you must send a request for permission first, and the amount of the transfer must be the same as the amount you applied for.

## Disable Token

Accounts can be disabled from receiving QCT or other types of tokens. In some cases, the user may want the address to receive a certain type of token only, but the sender has sent the wrong token, which leads to a lot of unnecessary trouble, we can disable the deposit of other tokens, and when the wrong token is deposited, they will receive an error warning message.

## Global Freeze

The issuer of a token may choose to freeze the circulation of its issued tokens, provided it has not disabled the ability to do so. After a global freeze is applied to a particular token, it will only be able to circulate between the user and the issuer, and it cannot be exchanged between users or be traded on the decentralized exchange, any pending orders will be considered invalid. If a secret key leak occurs to the issuer, this helps to block further losses, leaving time for the issuer to investigate or change the issuing address.

The ability of global freeze can be publicly declared to be abandoned. If the ability of global freeze is abandoned, it means that the token is allowed to be used more freely, and the issuer cannot intervene in the exchange of tokens.

# Applications

## Decentralized Identifier

Decentralized Identifier can be used for education, job resume, and identification of assets, which are issued by the issuer to the grantee in the form of verifiable credentials, some of these can be revoked by the issuer, and some cannot once they are issued, or they need to be jointly signed by multiple parties, or the grantee has the right to reject. These cryptography-based issuances of verifiable credentials have a wide range of usage, for example, a loan with insufficient collateral, if the debtor can provide verifiable credentials based on a Decentralized Identifier (these credentials are based on the trustless algorithm, which is considered to be unforgeable), that will prove his social credit.  There are also other usages such as the electoral system of "one person, one vote", the DAO governing voting(how to distinguish whether the voter is a real person or a robot), web3 creator rights protection, and the ownership verification of NFT. Applications can generate a Decentralized Identifier using the Qifas system. Qifas is not only a blockchain but also a plugin repository. With the help of plugins to encrypt identity information, and selectively display personal information. The demander can verify the desired user information through zero-knowledge proof and trustless algorithms. The user can completely control their personal information, for example, showing their age without disclosing birthdays. This can be used in issuing copyright to the author and verifying the identity of the creators. it allows users to transfer value between each other in a preset way without disclosing the trigger information that leads to value transfer. yet more innovations are being created, especially in controlling the security of personal data, increasingly, Dapps are gaining user trust by being audited by trusted organizations or through threat detection infrastructure.

**DID resolution**

The pattern of DID is: "did:"+<did-method>+":"+<method-specific identifier>. DID Method is a set of public standard operations through which you can create, parse, update and delete DIDs. These methods allow DIDs to register, replace, rotate, recover, and expire in the identity management system. DID resolver allows you to take DID as input and return the metadata related to DID Doc, which follows the data format definition such as JavaScript Object Notation (JSON) and its related link data JavaScript Object Notation (JSON-LD).

**DID Doc resolution**

The DID Doc resolution includes the unified resource identifier (URI), the authentication method of the DID that identifies the identity subject of the DID document, the public key used for authentication, the ownership proof algorithm of the DID, a set of authorization and delegation methods for the DID to allow another entity to operate on their behalf (i.e., the custodian), and the pseudonym or one-time DID mechanism for privacy protection.

**Verifiable credentials**

The verifiable credential is a tamper-proof certificate signed and encrypted by the issuer, which has the characteristics of cryptography security, privacy protection, and machine readability. A credential usually consists of at least two sets of information. One represents the verifiable certificate itself, including the metadata and declaration of the certificate. The second is digital proof, usually a digital signature. The attribute information associated with the verifiable certificate and the identity holder is based on the signature and verification of the cryptographic method to ensure the authority and privacy of the certificate. Verifiable vouchers also support revocable ability. The creator of the voucher is responsible for revoking the issued voucher through the revocation mechanism. The verifier needs to have the ability to flexibly and efficiently verify whether the voucher has been revoked.

## Encrypted Communication

Cross-chain communication, for example, uses a Qifas address to send messages to Ethernet address. This is a kind of point-to-point decentralized communication, which is different from the traditional communication method, it does not need to register a user or set a login password. After the information is encrypted by the sender, it is sent to the blockchain node. The blockchain verifies whether the sender forges the message and whether the message content and destination address have been tampered with. Note that the message is always encrypted during this process, even the nodes on the blockchain cannot decrypt the message. Only the receiver can decrypt the message, this is because only the receiver holds the secret key to decrypt the message.

## Decentralized Storage

The content server in Qifas is a decentralized storage data server, which is different from blockchain ledger storage. The content server is used to store DID information, and network information with synchronization requirements. The content server provides search, read, and write primitives. A new high-level storage primitive is also provided for nonvolatile (flash) memory (NVM). In an atomic operation, multiple I/O operations are batched into a logical group, which will be persisted as a whole or rolled back in case of failure. Moving the write atomicity down to the storage stack of the storage device, can significantly reduce the application workload. In this work, we provide a proof of concept to realize atomic writing on modern solid-state devices using the underlying log-based flash conversion layer (FTL). Using this new atomic write primitive can improve the system throughput by 33% and transaction response time by 20%.

## Decentralized Exchange

Qifas's built-in decentralized exchange can trade all kinds of tokens, including NFT. Users store their digital assets in their digital wallets and simply use decentralized exchanges to execute peer-to-peer trading.

**Automated Market Maker**

Automatic market maker (AMM) is a protocol used to provide liquidity. It is a smart contract that provides liquidity in the decentralized transaction. Each AMM holds a pool containing two kinds of assets and enables users to exchange between them according to the exchange rate set by the formula. AMM allows digital assets to trade automatically without permission through the use of liquidity pools rather than the traditional market of buyers and sellers. The liquidity pool is composed of asset pairs traded.
Price changes based on liquidity, for example, if the current price of company A's shares is 700, the liquidity pool will consist of $700 per share of company A's shares. Whenever trading occurs, for example, we buy some shares of Company A, we will add dollars to the pool and remove the shares of Company A, This will modify the relative price of Company A/USD trading pair in the liquidity pool.
Anyone can create an AMM for an asset pair that does not exist or participate in an existing one. Those who deposit assets in AMM are called liquidity providers and receive "LP tokens" from AMM. LP tokens can be exchanged for other assets in the AMM pool, including charging fees, voting to change the AMM fee settings, and weighing votes according to the number of LP tokens held by voters.
When the capital flow between the two assets in the pool is relatively active and balanced, expenses provide a passive source of income for liquidity providers. However, when the relative price of assets changes, the liquidity provider may have to bear losses. At any time, trading conditions are defined by the fluctuation of two assets in the pool. If these terms of trade are different from the market price, arbitragers will enter the market and adjust the size of the pool until it reaches equilibrium. Under the Automated Market, liquidity is supplemented by arbitrageurs, and the income belongs to the original liquidity providers. The market also runs well because the liquidity providers share the return of liquidity.

**How AMM Works**

AMM holds two different assets: at most one can be QCT, and the other can be a token. The issuer address and token symbol constitute the unique identification of the token. Each issuing address can issue multiple tokens. The same token identifier issued by different issuer addresses is considered a different asset. This means that AMM can be established for two tokens A.FOO and B.FOO with the same token identifier but different issuers, or tokens A.FOO and A.XOO with the same issuer but different token codes. We can switch the token order, the AMM from A.FOO to QCT is the same as that from QCT to A.FOO.
Each swap pool contains a pair of encrypted tokens, the most common one is QCT. Agents who want to provide liquidity for their preferred pool store both QCT and tokens in the pool. The proportion of QCT and tokens is determined by the proportion in the pool, which implicitly defines the QCT price of tokens. Agents who make such deposits will receive a certain proportion of liquidity tokens. With the transaction between the pool and users, the value of the liquidity pool may rise or fall. Liquidity providers can redeem their liquidity tokens at any time and pay their share in the liquidity pool with equivalent QCT and tokens. Providing liquidity may be profitable because each transaction faces a different basis point of tax, which will be deposited back into the fund pool.
AMM sets its exchange rate according to the asset balance in the pool. When you trade with AMM, the exchange rate will be adjusted according to the extent to which your transaction changes the balance of assets held by AMM. When the supply of an asset decreases, the price of the asset will rise; With the increase of asset supply, the price of the asset decreases. When the total amount in the asset pool is large, AMM usually provides a better exchange rate. The more unbalanced the transaction is, the more extreme the exchange rate will become. AMM also charges a certain percentage of transaction fees above the exchange rate.

![](https://github.com/qifashome/whitepaper/blob/main/images/Figure3.png?raw=true)



# Citations

1. Sam Newman: Building Microservices
2. Martin Fowler: Inversion of Control Containers and the Dependency Injection pattern
3. Erich Gamma: The journey of visual studio code: Building an App Using JS/TypeScript, Node, Electron & 100 OSS Components
4. Eric S. Raymond: The Cathedral & the Bazaar: Musings on Linux and Open Source by an Accidental Revolutionary
5. Xiangyong Ouyang; David Nellans: Beyond block I/O: Rethinking traditional storage primitives - 2011 IEEE 17th International Symposium
6. Uniform Resource Identifier (URI): Generic Syntax. T. Berners-Lee; R. Fielding; L. Masinter. IETF. January 2005. Internet Standard.