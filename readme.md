# Atomicity: A Peer-to-Peer Mutual Credit System

*Authors: John Carvalho, Ar Nazeh*

## Overview

Atomicity is a decentralized peer-to-peer credit protocol that allows users to issue, transfer, and settle credit without a central currency or authority. By relying on trust between peers, Atomicity enables flexible payments and credit settlements across any denomination.

## Credit Issuance

Each user is represented by a Pkarr-compatible public key. Any user can operate a private “ledger” channel state that allocates credit to any other key. When an entry is updated, the counterparty key is made aware of their new credit amount and its denomination by serving the data for asynchronous retrieval within a Paykit payment endpoint. This can also be posted directly to the peer.

Credit can be issued in two formats:
1. Signed state of the channel (example: IOU x-amount in total).
2. Conditional IOUs for routed transactions (see the Routing section below).

When a user receives a new signed state that includes the prior conditional IOUs, they can discard the conditional IOUs as they are no longer needed.

When a user receives a new signed state that is more recent than a previous signed state and with a higher IOU value, they can also discard older signed states as they are no longer needed.

This means that at most, a user needs to store (N + C), where N is the number of channels they have, and C is the number of conditional IOUs not yet merged into a more recent Signed State.

## Credit Settlements and Reassignment

Users can make signed payment requests to credit issuers. Upon receiving an authenticated request, the issuer checks the credit holder’s Paykit payment endpoints, then pays through a mutual payment method if possible.

Credit holders can also settle with the issuer by including extra Paykit endpoints, which are simply other Atomicity user keys. This allows the issuer to transfer credit from one user to another compatible user/key.

---

## Routing and Compatible Users

Atomicity utilizes **Pubky’s Semantic Social Graph** to establish dynamic and flexible trust relationships. Users can whitelist keys and set credit limits that they are willing to receive or issue to each peer key. They can also assign default settings for degrees of separation (based on the Semantic Social Graph), effectively establishing mutual credit channels with trusted peers.

### Routing and Conditional Payments

When making multi-hop payments, Atomicity uses conditional IOUs that depend on a secret (preimage p). For example, if A wants to pay D through intermediaries B and C, A can make the IOU to B conditional on B receiving the secret p. This prevents B from being owed money if D is not paid.

### Using Pubky's Semantic Social Graph for Trust

Trust in the network is based on Pubky's Semantic Social Graph, where users can define trusted relationships with others. This includes setting credit limits, tagging peers, and configuring preferences for degrees of trust.

### Commitment Mechanisms and Witnesses

For conditional payments, witness logs can be used to verify that p has been revealed before a certain time (timeout). These witness logs are decentralized and can be rotated or blocked by users if they fail to perform as expected.

---

## Finding Routes

To find routes from one user to another, the following process is used:

1. Everyone ready for routing publishes their immediate channels on a specific **Pubky** repository.
2. Routers crawl the network(s) and index all the routes for every node to every other node.
3. Routers announce themselves on a well-known, hardcoded infohash on Mainline.
4. If you want to make a transaction, you find a bunch of routers from Mainline (using BEP_0005 get_peers) and hope one of them is honest.
5. If a router performs well, you rate them positively so that your friends use them. If not, you move to other routers.

Routers, watchtowers, and aggregators of block lists (including ratings of witnesses) can help with the discovery process and enhance trust in the network.

---

## Delegated Payment Accounts

Using the same aspects of Paykit that facilitate subscription features, users can create delegated account keys for specific web accounts or other use cases, allowing those keys to access remote payment methods and credit lines for payments within an untrusted application.

## Examples

- **Example 1**: I assign a credit limit to my mom of $100, and I’m willing to accept up to $200 of credit from her. If my mom wants to buy a $5 coffee, but they only accept Lightning, she posts a settlement request for the Lightning invoice. I fulfill the Lightning invoice for her by providing proof that I hold $5 of her credit.

- **Example 2**: At dinner with trusted friends, I pay the entire $300 bill. I then post a request for $270 and share a QR code for them to pay me back. My app displays how much of the $270 has been paid. Once filled, I make the payment to the waitress. Some friends pay in credit, others to my Bitcoin address, and others to my Lightning node.

- **Example 3**: Mom is new to Atomicity, but I trust her to whitelist only trustworthy people. I assign a second-degree credit acceptance limit of $20 to her friends, allowing her network to use me as a possible route for payments when they lack compatible methods.

---

## What’s Unique?

Many aspects of this system combine features from existing systems, but it diverges from approaches like Offset. Paykit lacks the necessary logic to support both dynamic payment endpoints and credit as a settlement method. Pubky’s **Semantic Social Graph** also provides a more sophisticated trust model than traditional Web of Trust systems.

To meet these use cases, we need to build out ledger/channel formats, signing and requesting flows, and the Semantic Social Graph tagging and weighting system.

---

## Summary of Atomicity's Key Features

- **No Central Currency**: Users issue their own credit, allowing dynamic, personalized economies to form.
- **Decentralized Trust**: Pubky's Semantic Social Graph allows users to establish trust relationships and manage credit limits securely.
- **Flexible Routing and Payments**: Payments can be routed through multiple peers without requiring a single point of failure, and users can settle in any compatible method.
- **Delegated Payments**: Users can delegate certain keys to manage payments on their behalf, providing additional flexibility.
- **Privacy and Security**: All credit relationships and payments are handled securely with cryptographic signatures, and the decentralized trust network ensures that no single party can control or censor transactions.

---

## Semantic Social Graph Diagram

```mermaid
graph LR
    subgraph Users
        U1[User A]
        U2[User B]
        U3[User C]
    end

    subgraph Data
        C1[Post 1]
        C2[Post 2]
        C3[File 1]
    end

    subgraph Tags
        T1[Tag: Blockchain]
        T2[Tag: Decentralization]
        T3[Tag: Privacy]
    end

    %% Users tag content
    U1 --|tags with T1|--> C1
    U1 --|tags with T2|--> C2
    U2 --|tags with T2|--> C3
    U3 --|tags with T3|--> C1

    %% Users tag peers
    U1 --|tags with T2|--> U2
    U2 --|tags with T1|--> U3

    %% Content linked to tags
    C1 --|associated with|--> T1
    C1 --|associated with|--> T3
    C2 --|associated with|--> T2
    C3 --|associated with|--> T2

    %% Weighted relationships (represented by edge labels)
    U1 --|strong connection|--> U2
    U1 --|moderate connection|--> U3
