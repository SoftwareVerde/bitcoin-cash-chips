# Request Update to the Unconfirmed-Transaction Chain Limit

CHIP# *[TBD]*

## Authors

    John Jamiel, Software Verde
    Josh Green, Software Verde
    Doug McCollough, City of Dublin, OH
    Emil Oldenburg, Bitcoin.com
    Roger Ver, Bitcoin.com
    Mark Lamb, CoinFLEX


## About the Stakeholders

**Software Verde** is a custom software development company based out of Columbus, Ohio, USA that has been in operation since 2011 and working within public and crypto sectors since early 2017.  Software Verde has extensive experience working with local governments to promote the adoption of blockchain technology and utilization of cryptocurrencies, and is the author and maintainer of the BCH Full Node, Bitcoin Verde.

**City of Dublin, OH** is a municipality of approximately 50k residents that has made an investment into the adoption of blockchain technology.  In turn, Dublin has built a blockchain-based digital identity management system utilizing BCH SLP tokens as a reward mechanism.  In late 2019, Dublin’s identity management project moved into a beta-testing phase where Software Verde was tasked with creating digital IDs for city employees and rewarding them with tokens for their participation.

**Bitcoin.com** is a provider of Bitcoin Cash related financial services and the owners of the Bitcoin.com wallet, one of Bitcoin Cash’s most popular non-custodial mobile friendly wallets.  Their website provides important services such as a cryptocurrency exchange, reporting network related news, and providing information to help influence the growth and adoption of Bitcoin Cash and Bitcoin Cash related businesses.

**CoinFLEX** is a popular cryptocurrency exchange service as well as the providers of the first Bitcoin Cash futures and lending exchange. CoinFLEX is the primary distributor of Flex Coins, an SLP token used to pay dividends to their users. Their business provides several unique financial services that attract cryptocurrency investors to the network, as well as foster a culture of professional trading within the Bitcoin Cash community.


## Situation

When a transaction is first transmitted on the Bitcoin Cash network, it is considered “unconfirmed” until it is “mined” into a block.  These transactions that are not yet mined are also referred to as “zero-conf” transactions.  Transactions are dependent upon other transactions, such that they are chained together; the value allocated by one transaction is then spent by a subsequent transaction.  Currently, the Bitcoin Cash network only permits transactions to be chained together 50 times before a block must include them.  Transactions exceeding the 50th chain are often ignored by the network, despite being valid.  Once a transaction is submitted to the network, it cannot be revoked.


## Problem

When a transaction exceeds the unconfirmed transaction chaining limit, it is still considered a valid transaction, however it is often ignored by the network.  This leaves the value transferred by this transaction in an ambiguous state: it has been transferred, but some (or all) of the network may not record this transfer.  Once value has been transferred by a transaction, the balance may not be distributed in a different proportion or to a separate receiver (often known as a “double-spend”) due to the network’s convention to prefer the first-seen transfer rather than the newer transfer.  Therefore, the only viable path forward in this scenario is to transmit the same (perfectly identical) transaction again to the network.  However, if the wallet or service is connected to peers that accepted the transaction, rebroadcasting the same transaction does not cause the connected peers to retransmit it themselves--causing the transaction to be stuck with no recourse other than hoping to connect to a new peer that has not yet seen the transaction.  For this reason, it is important that all nodes agree on the unconfirmed transaction chain limit.  

Additionally, determining if the transaction was not accepted by the network is a difficult to solve problem with the current available toolset.  Error-responses from nodes rejecting a transaction have not been standardized, and often times nodes will silently reject the transaction--sometimes the node may not even be aware that the transaction is invalid because its dependent has not been seen yet, and the node itself cannot determine the transaction’s chain depth.

It is also not always known to the user/service/wallet how deep the unconfirmed transaction already is when it’s received; it’s possible that the coins they’ve received are already at the 50th limit, and determining that state can be near-impossible without a full-node.

The problem from a user/app’s perspective is that they have created a valid transaction and are given little indication that it will not be mined within a block.  The tools for recourse are limited, and the tools for monitoring for such a situation is also limited.

The unconfirmed transaction chain limit is mostly an artifact of an unused feature heldover from artificially restricting block size, a feature called “Child Pays For Parent”.  This feature is not used in BCH yet still restricts the user experience and increases the complexity of development of wallets and applications built on top of Bitcoin Cash.  


## Anecdotes

During a Dublin Identity beta test with real users, an issue occurred causing sign-ups to periodically fail.  After investigation, it was identified that users’ transactions from the server used to fund SLP tokens transfers were not being accepted by the network.  After further research it was discovered the reason the transactions were being rejected was due to the transaction chain limit being enforced by the network.

CoinFLEX uses SLP to distribute FLEX token dividends to its users.  The server distributes these dividends periodically, via chaining transactions.  These distributions were found to periodically fail due to reaching the unconfirmed transaction chaining limit.  CoinFLEX is mitigating this problem by using multiple UTXOs to spawn the chains, however their large user base and small limit of 50 transactions per UTXO, are causing disproportionate complexity within their system.  Raising the limit combined with increasing the base number of originating UTXOs helps to limit backend complexity.  Removing the limit would remove significant complexity for rejection edge-cases where a rejection was unable to be determined or was left unnoticed.


During BCH meetups it is not uncommon for users of the Bitcoin.com wallet to make more than 50 transactions within the timespan of a block, especially due to the encouraged behavior of brand-new users to transfer their BCH to other members of the meetup to “try it out”.  The user experience and “wow” factor of the technology is quickly doused when a new user’s transaction fails to send because their received UTXO is deeply chained.  Varying block times exacerbates this problem.


Software Verde has developed multiple applications that create and distribute transactions across the BCH network.  Managing multiple UTXO pools in order to handle appropriately scaling is doable but creates much unwanted complexity.  While transactions will likely never completely be “fire and forget” on BCH, creating a balance with a larger buffer (i.e. supporting a longer chain limit) and having better available tools would allow us to produce applications more reliably and for less cost, facilitating the adoption of Bitcoin Cash to businesses and enterprises.


## Request

Ideally, the unconfirmed transaction chain limit would be removed completely from BCH.  If it is deemed necessary or worthwhile to keep the unconfirmed transaction chain limit in some capacity then a significantly larger increase to the limit would be a reasonable alternative.  A 32MB block can hold approximately 135k transactions, which could serve as a hypothetical starting point for the unconfirmed transaction chain limit, although feedback from the protocol developers and developers and members of the greater BCH community is very much welcomed.  Additionally, increased tooling for determining a transaction’s current chain depth and standardization around error/rejection messages would enable non-protocol developers to be better equipped with taking on the responsibility of owning and monitoring the transactions they’ve transmitted.


## Timeline

The change to the unconfirmed transaction chaining limit is a network-change only, meaning it is only enforced at the network/p2p layer and therefore cannot cause a chain split.  This proposal is low risk and provides high benefit to the network as a whole.  For this reason, and the severity of impact caused by non-protocol developers, it is requested that this change be implemented in a coordinated manner as soon as possible.  We believe the most sensical timeline for that is the previous, although deprecated, date of May 15th.  Choosing this date in particular is not significant in and of itself, although there seems to be no reason to deviate from history purely for the sake of it.
