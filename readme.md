
**Atomicity**
A peer-to-peer mutual credit system

**Authors**
[John Carvalho](https://github.com/BitcoinErrorLog), [Ar Nazeh](https://github.com/Nuhvi)

**Overview**
Atomicity is a concept for an open peer-to-peer credit protocol that includes solutions for issuance, payments, settlements, and routing. 

It is assumed that trust will always be the most convenient and affordable basis for currency and that the most important questions are about whom to trust, and with what. 

There is no base unit or money in the system. Instead, it combines an abstracted extensible payment protocol, *[Paykit](https://github.com/slashtags/paykit/blob/spec/spec.mediawiki)*, with an open key-based censorship-resistant identity system, *[Pkarr](https://github.com/nuhvi/pkarr)*, and an *[Offset](https://docs.offsetcredit.org/en/latest/theory/mutual_credit_protocol.html)*-like mutual credit system, to establish incidental settlement for open credit issuance in any denomination.

By applying public-key-oriented identities and data to a credit system where anyone can issue any type of credit to anyone, we can create relative economies where people can dynamically and automatically choose whom they are willing to receive credit from, call/transfer debts, whitelist/blacklist, and make settled payments on behalf of others.

**References**
- Offset https://docs.offsetcredit.org/en/latest/theory/mutual_credit_protocol.html
- Pkarr https://github.com/nuhvi/pkarr
- Paykit https://github.com/slashtags/paykit/blob/spec/spec.mediawiki
- Rumble https://github.com/rumble-protocol/rumble
- Slashtags (tagging, web of trust)
- decentralized commit https://fiatjaf.com/3cb7c325.html
- commit methods https://ripple.ryanfugger.com/Protocol/Protocol06.html#commit-methods

**Credit Issuance**
Each user is represented by a Pkarr-compatible public-key. Any user can operate a private “ledger” channel state that allocates credit to any other key. When an entry is updated, the counterparty key is made aware of their new credit amount, and its denomination, by serving the data for asynchronous retrieval within a Paykit payment endpoint (could also be posted directly to the peer…)

Credit can be issued in two formats:

- *Signed state* of the channel *(example: IOU x-amount in total).*
- *Conditional IOUs* for routed transactions *(see Routing section below).*

When a user receives a new signed state that includes the prior conditional IOUs, it can ditch the conditional IOUs, as they are no longer needed.

When a user receives a new signed state that is more recent than a previous signed state, and with higher IOU value, it can also ditch older signed states as they are no longer needed.

This means that at most, a user needs to store (N + C) where N is the number of channels they have, and C is the number of conditional IOUs that is not yet merged to a more recent Signed State. No need to keep history.

**Credit Settlements**
Users can make signed payment requests to credit issuers. Upon receiving an authenticated request, the issuer checks the credit holder’s Paykit payment endpoints, then pays through a mutual payment method if possible.

**Credit Reassignment** 
Credit holders can also settle with the issuer by including extra Paykit endpoints that are simply other Atomicity user keys. This would enable the issuer to transfer credit from one user to another compatible user/key. 

**Routing, Compatible Users **
Atomicity utilizes the tagging method, of/like Slashtags, to establish whitelistings of keys, along with credit limits the peer is willing to receive and/or issue to each peer key. Users could also assign default settings for degrees of separation (WoT distance), and effectively establish mutual credit channels with states. 

While mutual credit channels are straightforward, creating a network out of them presents an extra challenge that Lightning Network does not face: [decentralized commit](https://fiatjaf.com/3cb7c325.html).

If A is trying to pay D through B and C, but she doesn’t want to Owe B before D is undeniably paid, it can obviously make her IOU to B conditional on B knowing a secret p (for preimage).

The problem is, for this system in practice, this knowledge of p has to also happen before a point of time (timeout), otherwise a transaction might settle years in the future, way after you thought it failed, and now you owe someone money for a purchase that didn’t go through.

But what if D shared p with C in time, so it assumes that now C owes him, so it goes ahead and sends the goods to A. But later C can deny that it got p in time, so it denies that it owes D anything.

The solution that mutual credit systems use is an agreed upon [commit method](https://ripple.ryanfugger.com/Protocol/Protocol06.html#commit-methods) which is typically either a trusted server or a blockchain.

[*Rumble*](https://github.com/rumble-protocol/rumble?tab=readme-ov-file#witness-commit-method) instead suggests that A says to B IOU, if and only if, you or someone else, shared the preimage p on one or more of these specific witnesses’ logs, before block number X (roughly equivalent to point in time).

Once A sees that p was published, it signs a new state of the channel with B and sends it to B.

These witnesses then:

1. Only trusted until A and B sign a new state and send it to each other.
2. Don’t see anything other the random nonce p 
3. Will be caught if they ever forge the history of the log, so they can’t take a publication of p back.
4. Can censor publish p in time to disrupt a payment, but then participants are free to use other witnesses in subsequent operations, and even block list these witnesses so A in the future knows that B simply won’t accept an IOU that is conditional on a Witness X so better use Witness Y instead.

Like all transparency logs, the proof of duplicity (forks) needs to be publicized somehow to hurt the operator. So some watchtowers that receive these proof of forks and consulted if they have seen a proof of forks for a certain Witness 

More about Routing:

To find routes from one user to another, we can follow this naive approach:

1. Everyone that is ready for routing publishes their immediate channels on a specific Slashtags repo.
2. Routers crawl the network(s) and index all the routes for every node to every other node
3. Routers announce themselves on a well-known hardcoded infohash on mainline
4. If you want to make a transaction, you find a bunch of Routers from Mainline (see [BEP_0005](https://www.bittorrent.org/beps/bep_0005.html) get_peers), and cross your fingers that one of them is an honest router, not just spam.
5. If they are nice, you rate them so your friends use them (until these routers go to jail and you use Mainline to find new routers)

Between routers, watchtowers, and maybe aggregators of block lists and ratings of witnesses (instead of you having to crawl all your friends and friends of friends' datastores), we can define one general server “Indexer?”... and these can be discovered from Mainline as explained above.

Note about [interactivity](https://github.com/rumble-protocol/rumble?tab=readme-ov-file#non-interactivity):

If you A wants to pay D but the later is offline, A can contact C instead and ask him for an IOU for D conditional on the publication p and then A can publish p so C can get paid by B. Whenever D is finally online (or through async message), A can send him both C’s IOU and p, so he is considered paid. 

**Delegated Payment Accounts**
Using the same aspects of Paykit that facilitate subscriptions features, users can create delegated account keys for specific web accounts or other use cases, allowing those keys to access remote payments methods and credit lines for payments within an untrusted application.

**Examples**

1. I assign a credit limit to Mom of $100 and I assign that I am willing to accept up to $200 of credit from Mom. I also assign which currency tickers I am willing to issue/receive credit in.  Mom wants to buy a $5 coffee, but they only accept Lightning. Mom’s app posts a settlement request for the Lightning invoice for the top person in her web of trust that she has credit with and also can pay out via Lightning. Mom posts a proof that I have $5 of credit in her ledger, then I fulfill the Lightning invoice she posted.
2. I am at dinner with trusted friends and I decide to pay the entire $300 bill for everyone to keep it simple for the waitress, but I still want everyone to pay me back. I post a request for $270 ($300, minus my meal!) and I share a QR of it to everyone there. Of course they all have our app, which opens and asks them how much they want to pay. I trust these friends, so I am willing to accept credit from them. My app displays how much of the $270 has been filled, and as soon as it is, I make the payment to the waitress. Some of the people paid in credit, some paid to my onchain bitcoin address, others paid to my Lightning node.
3. Mom is new to Atomicity, but I trust her not to whitelist anyone that is not trustworthy, so I assign a second-degree credit acceptance limit to her friends of $20. Now her friends can use me as a possible route when paying for coffees for which they might not have a compatible payment method, but I do. 

**What is unique here?**
Many aspects of this system are merely a combination of other existing systems, but Offset isn’t exactly the same credit approach and I’m not sure if their work is reusable. Paykit is lacking a lot of the logic to support both its current dynamic payment endpoints and credit as a settlement method. 

We would need to build in these interactions, the ledger/channel format, the signing and requesting flows, and the web of trust tagging & weighting, etc, to meet the described use cases and features.

There are of course more details that are not addressed in this document, a big one being how much privacy can be maintained in such a system, failure modes, as well as layers that could be added to the system like enforcement, insurance, DLCs, and more elaborate punishment and reputation systems. 

**Methodological Criticism** 
While trying to integrate as many payment networks as possible, and achieve the most generality (every user is a possible routing node), this approach doesn’t fit with what we keep learning about the value of minimalism, i.e asking what is the least amount of mutations on existing systems that could sufficiently fix their undesirable features. This approach is arguably not minimal, and can’t be so until we ask ourselves what risks in the “private trusted banks” paradigm are we trying to mitigate? and conclude that we can’t sufficiently mitigate such risks without a radical approach. Credibly exitable credit issuers (or resilient Hawala-like network) would be easier to reason about with clear roles and responsibilities.

*Counterarguments:*
We do not need to support maximum settlement methods as possible", we only need to allow them to be user-defined and mutual. Any matching will be inherent to the supported methods in the client applications. 

We've have to qualify the systems we are comparing somehow - as there are no proven solutions beyond Tether really, or at least this could be argued to some degree...

We are trying to mitigate the risk of misaligned incentives in trusted monies.

It's possible there are better ways to approach this and other roles that could be added or mutated in the design.
