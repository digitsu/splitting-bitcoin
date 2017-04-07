# Mitigating Bitcoin Forking Risk during Network Upgrade
How to Manage a Fork Split, a Guide for Exchanges and Businesses

Jerry Chan
@digitsu
[wallstreettechnologist.com](http://wallstreettechnologist.com)
12A8LKsf2qHnF95JXBmui8yBteestqquGz

v0.2.0
March 6th, 2017

[TOC]

### Abstract
With the Bitcoin community potentially at a cusp with some proponents favouring supporting their scaling solution at all costs at the expense of a system-wide consensus or compromise, the possibility of a fork split in Bitcoin is at its highest levels since inception. One thing is for certain if the current deadlock between different proposed scaling strategies persists, then the only thing that would result is the inability for Bitcoin to scale. In that light, a fork split, although unfortunate, would seem to be the best outcome for all parties involved. This document assumes that a fork split is thus likely to occur, and attempt to address the technical considerations of such a split on businesses and users operating on the Bitcoin blockchain.

The main impetus for this document is the recent suggestions emanating from the Core development community about an implementation of a User Activated Soft Fork or UASF, (one that can be executed with a *minority* of hash power supporting it, but with the support of a sufficient number economic nodes) which presents a ___clear and imminent danger___ to the stability of the network, especially for the exchanges and businesses that run on Bitcoin.  Due to the potential of the chain splitting into 2 or even 3 forks in the case of a User Activated Soft Fork, businesses and especially exchanges are in danger of losing customer's funds if they are not prepared to handle the situation particularly if a UASF were to happen without warning.  The purpose of this document is to prepare such businesses so that they can continue to protect their customers funds in such an event, regardless of whether they intend to support multiple fork coins or not.

## Types of Chain Splits
First we should enumerate the types of splits that can happen. Chain splits are _always_ caused by a hash rate minority enforcing validity rules that the majority does not enforce.
### Soft Fork
A soft fork is a software change where a new consensus rule is added to node implementations, making the new rules stricter than the previous rules. Blocks conforming to the newer more strict rules are considered valid by non-upgraded nodes, while blocks produced by old software under the previous less strict rules may not be valid according to the new rules.
### Hard Fork
A hard fork is a change to node implementations in which consensus rule is removed or relaxed. Blocks conforming to the new consensus rules are valid in the upgraded clients only, and may not be considered valid by old clients. Blocks produced by the older clients are valid in the new one.
### Minority Fork
This occurs when a _minority hash rate_ supported chain deliberately splits the network by rejecting a block which the **majority** hash rate supported chain accepts. The minority chain can arbitrarily create a rule, enforced only by them that makes all the future blocks produced by the majority chain invalid to them (User Activated Soft Fork). Minority forks are always soft forks.
### Majority Fork
This is when a _majority hash rate_ supported chain deliberately splits the network by publishing a block which the **minority** hash rate supported chain will not accept. Majority forks will always be hard forks.

### User Activated Soft Fork (UASF)
This type of soft fork is done when a majority of the user nodes (wallets, full nodes) upgrade to a version of the software which coordinates a block height after which a certain new validity rule is enforced, for example, a rule that would say that a block in which a UTXO that isn’t a paid to a new SegWit output is invalid.  This can be done ***without*** a majority of hash power supporting it. Generally speaking, it is a specific kind of Minority Fork. Where Majority Forks favour the side with more supporting hash power, UASFs favour the side with the most economic node support.

This kind of Minority fork has the additional risk of creating 3 resulting forks. Because it is a Minority fork that is instigated by the nodes, it implies that the opposing fork will have a majority of the hashpower at the time it happens. As a reaction to this and to protect themselves from re-org risk (see below), the majority hashpower would likely immediately execute a Majority Soft fork, thus resulting in 3 chains: The Minority soft fork, the Majority soft fork, and the original chain. This state should be temporary, as the resulting original chain would be eventually outpaced by one of the other soft forked chains and merged/re-org'd.

This type of fork has the potential for customers to not see withdrawals from exchanges and create customer support issues. Exchanges intending to support both forks should upgrade their node for this chain and ensure that withdrawal requests are processed through this upgraded node for the clients who demand it. Support of this policy implicitly means that the exchange is agreeing to support split coin products in the future. This means any remaining balance on the other chain for the client must be created and maintained within the exchanges accounting system. It would be advised that the exchange split the coins before processing any withdrawals to avoid confusing balances. (see splitting process below)

## Technical Features of Forks
### Re-org risks
The re-org risk is the risk that sometime after a fork one chain which was formerly the minority chain may somehow overtake the majority chain, and thus cause a reversion of that longer chain back into the minority chain, undoing all the transactions that were done in the chain since the fork. This actually happens naturally as part of normal Bitcoin network operations and re-orgs of a couple blocks deep a few times a week is not uncommon. The difference in the case of a split fork is that the re-org risk is one-sided.

Soft forks create a re-org risk on one forked side only (the other chain) due to the nature of a soft fork, namely that it is an added restriction in the validity rules for a block, it will never accept a longer chain produced by the other fork as it will deem it invalid. The converse is not true, because a block that adheres to the additional validity rule will still be valid on the original chain. Therefore there is a small but increasing chance (so long as the hashpower is greater) that the new more restrictive chain may out pace the original chain and cause a re-org to it. The converse is true for a Hard fork, which has a re-org risk on its own chain but not on the other chain. That is because blocks which are created by the other chain will always be valid on the hard forked one, but the reverse is not true, so a hard fork creates a chain which can be potentially re-org’d without the reciprocal risk on the other chain.

This risk is only a factor if the more restrictive chain has more PoW hashing power by a large enough margin such that it can outpace the original chain’s hashing power, and thus find more successive blocks in a row than the original chain. This re-org risk is something that the original chain must accept.  If this risk is deemed too excessive it may be possible that a one-time checkpoint be created after the Forking Block so that it cannot be re-org’d beyond this checkpoint.  One possible way of implementing this would be a soft-fork rule added which would make any block which does not contain the Forking Block as an ancestor invalid.

Re-org risk is balanced against the risk of following a minority chain. If one desires to always follow the most work chain, then re-org risk cannot be avoided. Adding a checkpoint to avoid re-org risk introduces the risk of following a minority chain and diverging from the economic majority.

### Split Coin Assets
Coins in existence at the point of the fork will become 1 of 3 possible types, pre-fork, post-fork Red, or post-fork Blue. It is easier to think of this in terms of UTXOs. A pre-fork UTXO can become a post-fork A or B UTXO, but not vice-versa (a post-fork A, or post-fork B UTXO cannot be reverted to a pre-fork UTXO). These resulting UTXOs can only exist on one chain or the other but not both.

A pre-fork UTXO may continue to persist, and be usable on both fork A and B, so long as they are not used. Once used in such a way and confirmed in a transaction on either Fork A or B, then the UTXO is said to be permanently split.

### Concerns of additional “value” created
This is the issue which made some people very concerned when Ethereum underwent a split. They were very well elaborated on in this [Coindesk article](http://www.coindesk.com/ethereums-hard-fork-created-new-kind-double-spend/). In summary though it is not possible to execute these quick ‘arbitrage’ opportunities in the case of a Bitcoin split, because splitting coins is not easy and require quite a bit of work on the part of any exchange or parties acting as one. Additionally, the supply of pure Chain A coins and Chain B coins will be very low initially and accumulate at a very slow rate. This rate is governed by the natural diffusion rate of newly mined coins in addition to the UTXO natural churn rate which will be greatly encumbered in the minority chain due to loss of hashpower and transaction processing capability. This prevents the massive speculative bets that were made on ETC which ended up losing a lot of money for the risky speculators who hoped that if they all bought ETC then most of the miners would follow.

### Double Spend Attacks
This is often quoted as a problem with a blockchain split, namely that any transaction valid on one chain is valid on both and thus there is an increased chance of double spending in the case of a split. This is achieved by taking advantage of the fact that your counter-party may be on one chain while you are on another. You can then spend on one chain, but then double spend the same UTXO on the other chain to pay yourself. This is a false attack, as the person who you are paying is generally not going to be interested in receiving payment on both chains.

### Transaction Replay
This sort of vulnerability is a variation of the double spend attack, and was first explained prior to the Ethereum split into ETC and ETH, and it affects mostly exchanges who were ill-prepared to handle both forks. It was explained in an [article](https://medium.com/@timonrapp/how-to-deal-with-the-ethereum-replay-attack-3fd44074a6d8#.s7tw2gpv8), how to prepare for this sort of attack. In summary, exchanges wishing to support both chains post-split and to facilitate trade between the 2 new coins as separate products needs to keep the balances of each split coins separate. In addition coin-separation processes (described below) must be put in place so that exchanges do not inadvertently send out any coins unintentionally.  This is especially important to those who wish to employ a coin separation strategy which manages the separation at the time of withdrawal.

## Considerations - Bitcoin Currency Exchanges
### Supporting a split
The general strategy to support both coins from an exchange is divided into several categories: 
1) **Running Nodes on Both Chains**
2) **Key Management**
3) **Procurement of Pure Split Coins**
4) **detection of the forking block**
5) **coin separation process**

#### Running Nodes on Both Chains
All exchanges should run both node clients of each fork in order to monitor activity in the both chains, and to prevent potential loss of customer funds. Running a node on both chains also allows you to efficiently manually split your coins, and also give you accurate balances of your hot wallets on both chains.  You should start running a node for each potential fork well prior to the actual fork.  That way you can use the balance of the hot wallets to monitor funds deposited from customers and which fork it is from.

#### Key Management
Exchange private key synchronization between all nodes on both sides on the chain are important.  Using the same hot wallet deposit addresses for nodes on both sides of the fork ensures that customers who accidentally deposit funds into the wrong fork can have their funds returned to them.

#### Procurement of Split Coins
Using split UTXOs derived from a coinbase txn from a block higher than the forking block for both chains are one way to separate customer deposits and to ensure withdrawals are not mixed. The exchange can get split coins either by purchasing pure coinbase generated coins directly from miners, or splitting coins themselves manually.

##### Procurement of Pure Coinbase
Buy coinbase straight from the miners from both forks chains, keep these in separate ‘pure’ coin pools (addresses), different for each fork. These source pools of pure post-fork coinbase coins will be essential in the process of coin separation. You must verify the provenance (history) of each UTXO to ensure that it is derived from a coinbase transaction from the respective chain ___after the forking block___. Be aware that the exchange only needs to procure one sample of a pure coinbase UTXO, as the exchange can then use this initial sample to convert its entire inventory to separated Red and Blue coins. If an exchange does not want to wash its entire inventory through splitting transactions, or cannot afford to, then it is advised that the exchange NOT support both chain coins separately.

##### Creating Split Coins Manually
The method to manually split coins is relatively simple, and can be employed to generate split coins without pure miner coinbase coins, although to be absolutely safe use 1 UTXO of purely split coin as per the "taint separation process". The exchange can decide to split its entire reserve or just the amounts to be put into the hot wallets when they are needed. The process is as follows: assume the exchange controls 3 addresses A, B, and R. A is an address with prefork (mixed) coins such as cold storage. Let's assume that there is both majority and minority chains, which we shall call Red and Blue respectively. B is the intended hot wallet address to store pure 'Blue' coins, and R is the address to store 'Red' coins. This example assumes the exchanges infrastructure is running on the majority chain, but the process can be altered if you prefer to operate mostly on the minority chain. 

- Starting with A (which may be cold storage), create a transaction that sends all coins to B, using a library where you can assemble a raw transaction using UTXOs, (such as Bitcore) mix in 1 purely split 'Blue' UTXO into the inputs of the txn called A->B. 
- Alternatively, one can create the transaction to be of size 999,999 bytes long, as this txn can only be valid on the >1MB chain.
- Post it to the network (which chain doesn't matter). Wait until A->B confirms on the Blue chain (it should confirm on same chain as the tainted mixed UXTO referred to in the inputs). 
- Then on the other chain's node, create an A->R transaction. Send this txn to the Red chain. 
- You now have purely split coins, Blue coins in B, and Red coins in R, from the original A address.

Note: It isn't necessary to use 2 different addresses, as you can just as easily keep both split coins in address B, but having different addresses may avoid confusion in some accounting software which would otherwise have to be run on both chains simultaneously.  Splitting the coins into 2 different addresses makes keeping internal accounting and reconciliation processes simple, as Blue and Red coins can be treated as 2 separate products.

#### Detecting the Forking Block
Knowing which block is the forking block for each chain is important.  The forking block is the first block to come after the last common block (LCB) shared by  both chains.  Detection of the forking block can be done by first finding the LCB shared between both chains.  This needs to be only done once. To do so one needs to query back from the chain tip on both nodes for block hashes, until the LCB is found. The forking block for each chain will be the block immediately following the LCB, respectively.

It should be mentioned that detection of the forking block is much simpler from a node running on the majority chain. This is due to the fact that the majority chain has the minority chain in the orphan pool and can scan its blocks. The minority chain, in contrast does not have the majority chain stored as their blocks were considered invalid and likely discarded.

#### Coin Separation Management
Exchanges intent on supporting both forks should split their hot wallets into 3 buckets, Red, Blue, and pre-fork 'neutral' sets. Management of both coins as separate products on the trading platform centres around the separation of the coins at point of deposit (or withdrawal) into (or from) the exchange, and ensuring that withdrawals are processed using only separated coins from the respective pools. 

Keeping coins separate involves 3 processes:
##### Deposit Process
Managing customer deposits in a split coin world involves simply giving customers a different deposit address for each chain, and monitoring which chain the deposits arrive on. This can be done by levering the fact that the exchange is running nodes and block explorers for both chains. Cases which need to be handled are when customer accidentally deposits the wrong coin into the deposit address and when customers deposit both Red and Blue coins simultaneously.  In both cases the exchange needs to simply contact the client and refund the coins on the appropriate chain from the exchanges pure coin reserves. 

A simpler method for exchanges that have no intention of supporting both chains is to just check the inputs of each deposit transaction and determine whether or not the parent block of all the inputs are from blocks which are younger than the forking block.  If they are, then there may have been coins deposited at the same address on the other chain (unless they were deliberately double spent), and it may be necessary to run a minority chain node in order to refund them to the customer.

##### Reserve Process
Managing the exchanges reserves of coins has 2 main strategies: 
1) Convert all the reserves at once
2) Convert them only when needed for withdrawal from hot wallets

The first option is to convert all the exchanges reserves to split coins via method described above on creating split coins. Alternatively, you can employ the Taint Separation process described below. This method allows for simpler accounting as balances for Red coins can be in stored on a certain HD wallet branch while Blue coins on a separate address tree.  Some reasons that this may be impractical would be if the exchange stores most of its reserves offline or in cold wallets, or at a vault wallet company.

The second option is leave the reserves as they are and rely on separating the pre-fork coins as part of the withdrawal process.  This is simpler but will mean that the accounting process may be a little bit more involved. The important thing is that in order to keep accurate balances of the inventories of each coin the exchange should make sure to run the back office system on both chains.

##### Withdrawal Process
The withdrawal of coins is the most important part of managing a split coin process.  This involves ensuring that the right coin is sent to the customer on withdrawals.  This means employing a coin separation process that is mandatory on withdrawals, which is only required if the exchanges main reserves have not already been separated.

##### Taint Separation Process
In order to separate outgoing coins, one can employ the "Taint Separation" process.  In this process, the withdrawal transaction is created manually on the chain that the customer is attempting to withdraw on, and an UTXO from the pure split coin address is added to the transaction to "taint" it.  The addition of a post-fork UTXO from one of the 'pure' UTXO pools ensures that the withdrawal transaction cannot be valid both Red and Blue chains at the same time.

### Exchange Customer Considerations
#### Existing coin balances policy
What happens to BTC balances (which are off chain) after a fork and an exchange decides to support both forks? Customers should be warned in advance that should a fork occur, the exchange will preserve the value of the coins in any forks created, but that in order to do so withdrawals will be withheld until the forks resolve back into 1 chain, or 2 chains emerge and persist past the point of reasonable re-org risk. (Each exchange would have to determine at which point they feel comfortable with assuming this risk, but a good estimate would be 100 blocks deep on the minority chain)

Any remaining coin balances at that point should be separated by one of the coin separation methods used to separate the exchanges own reserves. 1 pre-fork coin should equal 1 post-fork coin for each post fork chain supported. There should be no more pre-fork coin balances kept at this point forward

#### Wallet User considerations
If both fork coins are being supported, and withdrawals on one coin are supported then it must be stressed that the users receiving split coins must be using wallets which are running on a node which will recognize that split fork. 

SPV wallets will see potentially conflicting confirmations (at different block heights) for the same transaction depending on which nodes they are connected to. This shouldn’t matter as far as confirming the coins are received.
Wallets which are running on one chain or the other will only see the transaction confirmed if it is watching the correct chain.
All wallets will be able to see the transaction (0-conf) if it is a prefork UTXO being spent. If it is a split coin, then it will not see the transaction if watching on the other fork.

Customer support requests for withdrawals on one chain not showing up on their wallet should be expected. This will likely be a non issue, for retrieval of the coins will be as simple as changing the wallet to read blocks from the other chain. No coins are actually lost, they are just not visible.

### Not Supporting a Split
Supporting only the longest chain is the simplest option as no specific process to separate coins need to be supported. Process withdrawals and deposits only one fork, ignore the other. However this *may* put customers funds at risk.

Even if an exchange has no intention of publicly supporting the trading of both fork coins, if both chain split forks persist past 100 blocks, exchanges still have the duty to *process withdrawals* for clients that expect to own both chain coins. Supporting this policy means to follow the **coin separation management policy** as described above in order to make available the forked coins to be withdrawn from the exchange.

This policy is safe, as long as it is made aware to the customers. Even though an exchange has no intention of supporting both fork coins, there is the potential issue that customers who did not know this policy and tried to deposit Red coin into the exchange, which the exchange does not recognize or credit. How this will be treated is up to the policy of the exchange, but in the interests of protecting the funds of the customers, each exchange should adopt a coin separation policy as described above under the “Supporting a Split” section.

## Considerations - Wallet Users
Users have the option of totally ignoring the split or trying to capitalize on the split by selling the coins that they do not support to extract value. Keeping your coins separate would require using a wallet that would support each chain. Whether or not a wallet supports the minority fork will depend on the wallet.

Most importantly for wallet developers if they choose to support both chains is ensuring a good source of market data for displaying the price of the minority coin. It is through this relative price difference that users will have a hint of when they may be paying with the wrong coin.  

Additionally, if a server based wallet is connected to the minority 'Blue' chain whether through accident or negligence, the customer may be made to think that they receiving coins, only to not see them show up in their wallet balance.  In this situation the best course of action is have the developer of the wallet upgrade the software to support the majority chain, at which point the users funds will be made available again.  If the wallet developer is unwilling to change the software, extracting the coins on the other chain from the wallet may prove difficult.  For this reason it is advised to only use wallets that support the longest proof-of-work, or majority hashpower chain.

#### Web Wallets
You are at the mercy of which chain the web service will support. It will be doubtful that they will support both, most likely just the longest chain. They may be able to provide the ability to choose which chain you want to connect to should they decide to support both as a service.

#### SPV Wallets
SPV wallets by default will not be aware of the block size and thus just get the highest block height. Unfortunately ***if*** the SPV wallet connects to ALL nodes from one chain only, then it will report a balance that may differ from if it were to connect to all nodes from other chain. If it connects to a mix of nodes, then it will report the balance on the longest chain. (this is why using the 'longest chain is correct' policy is relatively safe).  

#### Hardware wallets
This depends on the specific wallet as some can be paired with your own node while some use a server back end. Find out which category your hardware wallet belongs to and refer to that wallets section for further advice. You should be able to setup the wallet to attach to the chain of your choice.  Knowing which chain your hard wallet supports is important because if you needed to confirm any transaction moving funds out of the hard wallet you will need to know which chain to watch.  In practice, as any coins present in a hard wallet at the time of a fork will become pre-fork coins, sending out funds from the wallet is relatively safe.  Receiving funds into your hardware wallet after the fork may cause some issues if the hardware wallet is not listening on the chain that you expect.  In the worse case scenario any funds sent on the wrong chain will result in loss of the funds unless the hardware wallet developer decides to add support for the other chain.  If you wish to use hardware wallets, it is suggested that you make sure you only use them for the chain that they will support.  (most likely the longest chain).

#### Server based wallets
If you can control which node the wallet points to, you will have to direct your wallet to use a known node that is on the minority chain. If you cannot control the server that your wallet connects to then you will be locked into transacting on the chain that your wallet provider is supporting. They may be able to provide details in order to choose which chain you want to connect to should they decide to support both.
#### Node client wallets
The easiest of the options, you can just run the software of the chain you wish to connect to. For users who wish to split their own coins so that they can send them separately, use of a node running on both chains is a necessity.

## Considerations - Businesses and Payment Processors
As a business, the best option is to follow the longest chain. Supporting both chains will be confusing to your customers and cause support issues as customers accidentally send you prefork coins and you would be required to refund them back separated coins on the fork that they did not intend to pay with. Unlike exchanges, supporting a minority chain does not generate additional profit for a business as it does for an exchange which can stand to make more trading fees. Supporting the minority chain is only a net cost to your business. But if you want to support both chains for ideological reasons, follow the same instructions on keeping your coins separate found in the Exchange considerations section.  One recommendation is to run a client node for each of the major forks in order to maintain visibility into the other chain.  This will assist in managing customer satisfaction in the case where clients accidentally pay with the wrong coin, and a refund needs to be processed.  This is a necessity for merchant payment processing businesses.

Additionally merchant processors will be unable to support the minority chain unless there is a reliable source of market data for exchange prices for the minority coin.  At any rate it will be a risky endeavour to support it beyond the ability to process a customer refund if an accidental payment is made.

## General Fork Risks
### UASF Risk
The employment of UASF in order to activate a fork without the majority of the hashpower carries with it some inherent risks that a normal majority fork does not.  Namely, that it opens up the possibility of creating up to 3 forks at least temporarily.
 
One possible scenario is that if the UASF is initiated by an influential party resulting in a minority soft fork activating SegWit, then this may trigger a third hard fork being created from the remaining network due to the latent demand for a hard fork which would increase the block size limit (for example, to 4MB). This is made possible by the initial UASF because if presumably all the supporters of SegWit would have forked off into their own chain, the proponents of a block size increase hard fork would no longer have any opposition and the hashpower remaining which would support the hard fork would be in the majority, sufficient to trigger a safe hard fork. This would result in 3 forks, a SegWit fork, a 4MB fork, and the original chain.  This 4MB fork would likely create a checkpoint post forking block to eliminate the re-org risk from the SegWit fork, leaving only the original chain vulnerable, and thus putting an additional incentive for remaining participants to join either the 4MB fork or the SegWit one.  Nevertheless, for a short time there may be 3 chains live at a given time, which makes it even more important that businesses prepare their systems for this possibility in order to protect the value of their customers deposits.

### Speculation Risk
Whenever a chain splits resulting in (potentially temporary) new coin balances, there will be a some traders who will try to capitalize by buying up the supply of the minority (cheaper) split coin in the attempt to profit from the exchange, or selling all the supply of one split coin and buying the other (if one has a view on which way the fork will resolve).  This is a *HIGHLY RISKY* activity and should be avoided unless one is prepared to lose all their value.  The safest way to preserve your value during a coin split is to not move any of their coins until the split has been resolved, either by collapsing back into one chain, or with 2 stable new chains emerging.


## Glossary
* Forking Block
This is the block that causes the fork split in the chain, and its complement block on the other chain. Presumed to be the first block which is viewed as invalid to one side of the network, but valid to the other. In other words it is the first block that both chains have respectively, that are not in common ancestry.

* UTXO
These are unspent transaction outputs, generated by transactions. The set of UTXOs in a node's memory is the sum total of all the coins available to be spent in the system (excluding unspent coinbase generated coins).

* Prefork UTXOs, Prefork coins
These UTXOs were created from blocks leading up to, but not including, the Forking Block. These are sometimes referred to as ‘mixed UTXOs’ because they can be used on both Red or Blue.

* Red coins, Blue coins, collectively ‘post-fork UTXOs’ or ‘split coins’
These UTXOs were minted in coinbases from blocks starting from the Forking Block and afterwards. If an UTXO can trace back to a coinbase generate transaction that is minted from the Forking Block or afterwards, they are considered ‘split coins’. As there are 2 sets of them, one for Chain A and one for Chain B, they should be treated separately and for all intents and purposes, they are separate coins.

* Coins
For the purposes of this document, the term is sometimes used to be synonymous to UTXO, as the context will indicate.

* Coinbase
Otherwise knows as “Generation transactions”, these are coins which are created directly from the minting of the block as their prior transaction.

* Node
Client software which connects to and participates on a given consensus network. This is sometimes used synonymously to mean client node software with a block explorer.

## Previous Publications
* Hard Fork Risk Analysis
http://www.wallstreettechnologist.com/2016/11/18/hard-fork-risk-analysis-if-the-worse-happens-how-bad-can-it-be/

* Emergent Consensus - a Guide to Forking Safely
http://www.wallstreettechnologist.com/2016/10/14/emergent-consensus-guide-to-forking-safely/

* Bitcoin XT and the Hard fork that will split us all
http://www.wallstreettechnologist.com/2015/08/19/bitcoin-xt-vs-core-blocksize-limit-the-schism-that-divides-us-all/

* SegWit Pros and Cons
http://www.wallstreettechnologist.com/2016/12/03/core-segwit-you-need-to-read-this/

* Lightning Network - Will it Save or Break Bitcoin?
http://www.wallstreettechnologist.com/2016/10/03/lightning-network-will-it-save-bitcoin-or-break-it/



