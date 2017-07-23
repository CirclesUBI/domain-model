# Domain Model Diagram
Old version: https://drive.google.com/open?id=0B-83nTx9NXI6TXg5azNlM3RBZ2s 

Concepts in grey will not be included in the MVP version of the platform.
## Preliminaries
Basic understanding about blockchains, in particular smart contract platforms like Ethereum
## System Overview
The Circles system consists of [personal currencies](#personal-currencies), [account holders](#account-holder), and [social graph](#social-graph), faciliated through a blockchain-linked application. Each user is associated with a unique, personal currency. At regular intervals, equal units of personal currency are created and added to the account of each user in the system. Users choose whose currency to accept by forming virtual relationships called [trust connections](#trust-connections). These connections also make longer paths of exchange possible, by serving as intermediary parties for [transitive transfers](#transitive-transfer).


## Social graph
●	The social graph of circles is a public datastore of weighted trust connections.

● A trust connection is a triple, (Alice, Bob_coin, 24) : Account x Currency x Int, with the intended meaning that the account holder (Alice) is willing to exchange up to 24 units of the Bob_coin currency in exchange for the same amount of any currency that she owns over one time period.

## Personal currencies


## Account Holder

●	An account holder is either a user or an organisation. 

●	An account holder is identified by a human-readable identifier (string). 

○	This identifier is used to easily identify the account holder when making a payment to that account holder, or when requesting a payment from them.

○	This identifier makes it easier for people to find account holder through the system’s search functionality. 

○	An account holder can choose their own identifier, as long as it’s unique and complies to certain validation rules (length, allowed characters, …).

■	Note that this may pose a security threat, if someone uses an identifier that was until recently used by someone else. A restriction could be added, blocking account holders from reusing identifiers for some time, or indefinitly, to prevent this. 

●	An account holder is the owner of an account with zero or more currency balances.

○	An account cannot have more than one balance for a given currency.

○	A balance consists of the history of transactions that (1) the account holder has participated in, (2) transacting an amount of the balance’s currency.

○	The balance bottom line can be calculated by adding up all transactions in its history.  Note: we could rename the term “bottom line” to “balance”, and use “ledger” or “account ledger” for the current “balance” term instead. 

●	An account holder owns the private key which corresponds to the public key of the account. 

●	An account holder H can choose to trust an issuer S (= create a trust relation to that issuer), meaning 

○	that H agrees to accept S currency for payment

○	that H agrees to mediate for payments in S currency to account holders that do not accept S currency themselves 

■	Payment mediation means that H is willing to exchange S currency for other currencies that are accepted by the receiving account holder.

■	Note that we may need to restrict payment mediation to prevent the mediator from being drained of currencies that are widely trusted.

●	This can be by calculating an exchange rate based on the number of trust relations to S (indicating its value to the mediator),

○	What about sybil trust relations? I can create fake accounts just for the purpose of collecting more trust relations.

○	This could be solved by only counting common trust relations, or another metric expressing “closeness.”

●	Or by limiting the amount that the mediator will exchange (in absolute or relative terms),

●	Or by asking the mediator for explicit permission for each exchange, or only for risky exchanges (whatever the criterion for a risky exchange would be)

●	Using their private key, an account holder H can make payments from one of their balances in S currency to a receiving account holder R, if 

○	the bottom line of the balance in S currency of H will not go below zero when it is debited 

■	the receiving account holder R accepts S currency, i.e. 

●	a direct trust relation exists from R to the currency’s issuer S,

●	or R is the issuer of S

■	or enough mediating account holders Mi can be found

●	who accept S currency

●	and who can exchange S currency for another currency Si, that is trusted by R
such that the total exchanged value matches the required payment value.

●	Account holders can create and accept offers to sell and buy goods and services.

●	An account holder has a default currency, which is the currency that they (1) prefer for receiving payments in, and (2) use to price their offers.

○	If the account holder is a user, the default currency is their user currency

○	If the account holder is an organisation, the default currency is the group currency of the containing group


## Users

●	Every Circles user is uniquely identified by their primary e-mail.

●	A user has a user profile with additional information such as first name, last name, picture, and so on. 

●	A user can authenticate through any of the authentication methods that they have enabled: 

○	Native login, using their primary e-mail and a password. 

○	Through their Google or Facebook account.

○	Through uPort, a decentralized identity provider implemented on the Ethereum blockchain.

●	Each user is an account holder, owning an account with zero or more currency balances. This also allows to create offers.

●	Each user is an issuer of their own (user) currency. This does not mean they can mint their own currency at will. Instead, a new amount of their currency is minted by the system at regular intervals, and added to the issuer’s account (= UBI).
Organisation

## Organisations

●	Every Circles organisation is uniquely identified by their name.

●	An organisation is initially created by a user. This user becomes the first administrator of the organisation. Subsequently, 
other users can be added as administrator.

●	An organisation belongs to a group. When a user creates the organisation, they can choose 
○	to put it in any existing group that the creator is a member of, 
○	or create a new group to contain the organisation (the user will then become the creator/first administrator of this group).

●	An organisation has an organisation profile, containing additional information such as their website, email, and tagline.

●	An organisation is an account holder, owning an account with zero or more currency balances. This also allows to create offers.

●	Organisation administrators: 

○	Can edit the organisation profile.

○	Can add or remove other users as administrator. 

○	Can request or make payments, establish trust relations, and other actions associated with being an account holder, on the organisation’s behalf.

○	Can move the organisation to another group. 

■	This means that the default currency of the organisation will change into the new group currency. The system may propose to correctly convert any offer prices using the exchange rate between the group currencies.

●	The actions of administrators can later be more finely controlled by adding systems such as (1) rights & permissions, (2) voting decisions and (3) time-delayed actions. For the MVP, we’ll leave this out. 

●	An organisation is not an issuer, so they don’t have their own currency. (However, the group that contains an organisation is the issuer of the organisation’s default currency.) 

●	An organisation does not receive a UBI.  

●	An organisation cannot authenticate itself to perform actions; it is instead fully managed by its administrators, who can act on its behalf.

## Issuer
●	An issuer is either a user or a group.  

●	An issuer issues their own currency.  

○	This does not mean that the issuer can create their currency at will. It needs to be minted as UBI by the system in case of a user, or converted from a user currency in case of a group. 

○	A currency issued by a user, we call a user currency. A currency issued by a group, we call a group currency. 

●	An issuer is trusted (not trusted) by an account holder if a trust relation from that account holder to the issuer exists (does not exist).

Blockchain

The blockchain is the global ledger of all transactions in the system. 

●	A transaction is an atomic unit. This means that it either succeeds or fails, in its entirety. It cannot partially fail, resulting e.g. in a payer’s account that is debited while the receiver’s account has not been credited. 

●	There are three types of transactions: 

○	minting transactions, 

○	conversion transactions,

○	and payment transactions.

Minting Transactions 

A minting transaction creates new User currency for a user.
Conversion Transactions
A conversion transaction transforms any arbitrary User currency into Group currency and credits it to the account holder that initiated the transaction. The ability for an account holder to create conversion transactions is defined by the Group’s membership rules (See the Groups heading above).
Payment Transactions

A payment transaction occurs when an account holder (the payer) sends currency to another account holder (the receiver), possibly mediated by third parties (mediators) while in transit. A payment transaction is made up of a series of three possible operations that make up the full transaction:

●	Every transaction has a Pay operation. This is the payer’s input of currencies into the payment transaction.

●	Every transaction has a Receive operation. This is the receiver’s output of currencies from the payment transaction.

●	A transaction can contain 0 or more Mediation operations in case the payer does not have enough currency that is accepted by the receiver. 

○	A mediator can be any account holder who accepts any currency from the pay operation and possesses currency accepted by the receiver.
○	The mediator can add new currencies as input to the transaction and take existing currencies as output from the transaction in exchange. There can be an arbitrary number of mediation operations between the pay and receive operation.
○	For the MVP, we’ll restrict mediation to direct conversions of pay operation currencies that are not accepted by the receiver into currencies that are accepted by the receiver. Note that this means that the mediation path is restrained here for simplicity. Future versions may expand on this. E.g. to go from currency A to C, one could first convert A to B and then convert B to C (i.e. indirect conversions).

Mediation Pathfinding Algorithm

The mediation algorithm needs to be defined in more detail before it can actually be implemented. Technically, this is a pathfinding algorithm solving a quite complex optimization problem.
It needs to determine: 

●	Which amounts of which currencies will be debited from the payer’s account.

●	Which amounts of which currencies will ultimately be credited to the receiver’s account.

●	Which mediators, if any, are selected.

●	Which amounts of which currencies are exchanged by each mediator.

The mediation algorithm could optimize: 

●	For the lowest number of mediation operations (hops), preventing complex payment transactions

●	For the lowest number of mediators, preventing fragmentation of small currency amounts

●	For the lowest amounts each individual mediator needs to exchange, preventing mediator accounts from being flooded with currencies that are not widely trusted

Akin to a GPS app suggesting alternative routes (fastest, shortest, most scenic, …), several payment transaction paths could be suggested by the system, optimizing for these different metrics. Unlike GPS paths which can be choosen solely by the driver however, not only the payer but also the mediators and the receiver are stakeholders here.

If the problem is rephrased in the correct terms, a version of the A* algorithm can easily be applied. A draft of one possible version is provided here: 

●	Nodes: 

○	The initial transaction state (i.e. after the inputs of Payer have been added), is the start node.

○	The final transaction state (i.e. after the outputs of Receiver have been removed), is the goal node.

○	Transaction states after the inputs & outputs of suitable Mediators have been added & removed are the intermediary nodes We assume that mediators exchange all the currency amounts that they can exchange.

●	Neighborhood function

○	If there are any currencies left in the transaction state that are not accepted by the receiver, the neighboring states are any transaction states resulting after applying a mediation operation by a mediator who accepts any of the remaining currencies not accepted by the receiver.
Additionally, if only direct transactions are supported, the condition can be added for mediators to have at least one (strictly positive) balance that is accepted by the receiver. This will prevent the pathfinding algorithm from branching over the entire user base.

○	If there are no unaccepted currencies left in the transaction state, the only neighbor node is the final transaction state.

●	Distance function

○	The distance between two nodes is defined as the difference in total unaccepted currency value of the corresponding transaction states. In the future, exchange fees can be added to this metric.

○	Taking the payment transaction diagram at the beginning of this section as example, the unaccepted currency totals and distances to the goal node X are: 

■	| B, X |  = (sum of unaccepted currencies after pay operation B) - (sum unaccepted currencies at X) = (A amount + B amount + C amount) - (zero by definition) = (3.50 + 10 + 5) - 0  = 18.50

■	| C, X | = (3.50 + 10) - 0 = 13.50

■	| Y, X | = 3 - 0 = 3

■	| Z, X | = 0

○	Distances between the subsequent nodes are: 

■	| B, C | = (3.50 + 10 + 5) - (3.50 + 10) = 5

■	| C, Y | = (3.50 + 10) - 3 = 10.50

■	| Y, Z | = 3 - 0 = 3

## Offers
Creating an offer

An offer is a proposed exchange of currency for a good or service. 

●	Any user can create an offer that is associated directly with their account. 

●	Any administrator of an organization can create offers that are associated with the organization’s account. 
Each offer is made up of:

●	An offer id

●	A title, text description, and an optional photo

●	A price that is expressed as an amount

○	Note: The price is not currently denominated in any specific currency. Rather, it is generically priced in terms of any currency that the seller accepts. This is intended only for the MVP because of the lack of fungible group currency. The implication here is that all User currencies that are accepted by the seller are equivalent in value.

○	The price is denominated in the account holder’s default currency, that is: 

■	in the user currency, if the account holder is a user

■	in the group currency of the group that contains the organisation, if the account holder is an organisation

●	An offer unit, which can be one of these types:

○	A single item (this offer will be removed from the system when filled)

○	Per item

○	Per hour

○	Per day

Finding an offer
Offers are indexed in the system by their owning account holder. 

●	In the MVP, in order to find an offer, the buyer should first look up the account holder by unique identifier string. The buyer can then browse their available offers on their profile.

●	In the future, multiple improvements are possible: 

○	Creating a search index so users and organisations can also be found by any information on their profile (first name, last name, organisation e-mail, …)

○	Search directly for offers, that can be indexed by their actual metadata contents (e.g. description, title, keywords) to allow buyers to search for different sellers offering the same products in their area. 

○	The Point-Of-Sale experience can also be streamlined by QR codes, deep links, or NFC chips to reduce friction.

Accepting an offer

When a user finds an offer, they can choose to purchase the good or service. This will automatically create a payment transaction that can be confirmed and sent like normal.

●	The memo of the payment transaction will be the offer’s unique id, for the purposes of bookkeeping.

●	The system will create the inputs and outputs to the payment transaction according to these rules:

○	The selected currency for the payment transaction will default to the buyer’s own currency.

○	If the seller does not accept the buyer’s currency, the system will check the buyer’s balances, sorted descending by amount, until it can find a currency that the seller accepts. 

○	The prefered currency for (the receive operation of) the payment transaction will be the seller’s default currency.

○	If the buyer does not have the seller’s default currency, or the amount is not enough, the system will check the buyer’s balances, sorted descending by amount, until it can find one or more currencies that the seller accepts (such that the total value matches the price.)

○	If the buyer does not have any currency accepted by the seller, or the total amount is insufficient, the system will create a mediated payment transaction to acquire currency that the seller accepts.

○	If no mediated payment transaction can be created, either because no mediators are available or the buyer’s total account value is insufficient, the transaction will be cancelled.

