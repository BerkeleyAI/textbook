---
title: 16. Bitcoin
parent: Cryptography
nav_order: 10
---

{% comment %}
Draft I found in the fa19 repo. Incomplete and not sure if it ever was finished.
We should finish it this semester -peyrin sp21
{% endcomment %}

# Bitcoin

## Problem Statement

Bitcoin is a digital cryptocurrency, which means it should have all the same
properties as physical currency (e.g. the United States dollar). In our
simplified model, a functioning currency should have the following properties:

- Each person has a bank account, in which they can store units of currency they
  own.

- Alice cannot impersonate Bob and perform actions as Bob.

- Any two people can engage in a _transaction_. Alice can send Bob $$n$$ units
  of currency. This will cause Alice's bank account balance to decrease by $$n$$
  units, and Bob's bank account to increase by $$n$$ units.

- If Alice has $$n$$ units of currency in her account, she cannot spend any more
  than $$n$$ units in any transaction.

In traditional physical currency, these properties are enforced by a trusted,
centralized party such as a bank. Everyone trusts the bank to keep an accurate
list of people with accounts and the balances in each account. If Alice sends
$$n$$ units to Bob, both Alice and Bob trust that the bank will correctly
decrease Alice's balance by $$n$$ and increase Bob's balance by $$n$$. Everyone
also trusts that the bank will not let Alice spend $$n+1$$ units of currency if
she only has $$n$$ units in her account.

The goal of Bitcoin is to replicate these basic properties of a functioning
currency system, but without any centralized party. Instead of relying on a
trusted entity, Bitcoin uses cryptography to enforce the basic properties of
currency.

## Cryptographic Primitives

Bitcoin uses two cryptographic primitives that you have already seen in this
class. Let's briefly review their definitions and relevant properties.

A _cryptographic hash_ is a function $$H$$ that maps arbitrary-length input
$$x$$ to a fixed-length output $$H(x)$$. The hash is collision-resistant, which
means it is infeasible to find two different inputs that map to the same output.
In math, it is infeasible to find $$x \neq y$$ such that $$H(x) = H(y)$$.

A _digital signature_ is a cryptographic scheme that guarantees authenticity on
a message. Alice generates a public verification key $$PK$$ and a secret signing
key $$SK$$. She broadcasts the public key to the world and keeps the secret key
to herself. When Alice writes a message, she uses the secret key to generate a
signature on her message and attaches the signature to the message. Anyone else
can now use the public key to verify that the signature is valid, proving that
the message was written by Alice and nobody tampered with it.

With these two cryptographic primitives in mind, we can now start designing
Bitcoin.

## Identities

Since there is no centralized party to keep track of everyone's accounts, we
will need to assign a unique identity to everyone. We also need to prevent
malicious users from pretending to be other users.

Every user of Bitcoin generates a public key and private key. Their identity is
the public key. For example, Bob generates $$PK_B$$ and $$SK_B$$ and publishes
$$PK_B$$ to the world, so now his identity in Bitcoin is $$PK_B$$. When Bob is
interacting with Bitcoin, he can prove that he is the user corresponding to
$$PK_B$$ by creating a message and signing it with $$SK_B$$. Then anybody can
use $$PK_B$$ to verify his signature and confirm that he is indeed the $$PK_B$$
user. Because digital signatures are unforgeable, an attacker who doesn't know
Bob's secret signing key will be unable to impersonate Bob, because the attacker
cannot generate a signature that validates with $$PK_B$$.

## Transactions

Without a centralized party to validate transactions, we will need a way to
cryptographically verify that Alice actually wants to send $$n$$ units of
currency to Bob. Fortunately, this problem is essentially solved with our
identity scheme above. If Alice wants to send $$n$$ units of currency to Bob,
she can create a message "$$PK_A$$ sends $$n$$ units of currency to $$PK_B$$"
and sign it with her secret key. Note how she uses her public key $$PK_A$$ as
her identity and Bob's public key $$PK_B$$ as his identity. Now anybody can
verify the signature with Alice's public key to confirm that the user $$PK_A$$
did intend to make this transaction. Bitcoin doesn't validate the recipient--if
someone wanted to refuse a transaction, they could create another transaction to
send the money back.

## Balances

In our transaction scheme so far, nothing is stopping Alice from creating and
signing a message "$$PK_A$$ sends $$100n$$ units of currency to $$PK_B$$," even
though she may only have $$n$$ units of currency to spend. We need some way to
keep track of each user's balances.

For now, assume that there is a _trusted ledger_. A ledger is a written record
that everybody can view. It is append-only and immutable, which means you can
only add new entries to the ledger, and you cannot change existing entries in
the ledger. You can think of the ledger like a guest book: when you visit, you
can add your own entry, and you can view existing entries, but you cannot (or
should not) change other people's old entries. Later we will see how to build a
decentralized ledger using cryptography.

Bitcoin does not explicitly record the balance of every user. Instead, every
completed transaction (along with its signature) is recorded in the public
ledger. Since everyone can view the ledger, anybody can identify an invalid
transaction, such as Alice trying to spend more than she has. For example,
suppose Bob starts with $10 and everyone else starts with $0. (We will discuss
where Bob got the $10 later.) Consider the following ledger:

- $$PK_B$$ (Bob) sends $$PK_A$$ (Alice) $5. Message signed with $$SK_B$$.

- $$PK_B$$ (Bob) sends $$PK_M$$ (Mallory) $2. Message signed with $$SK_B$$.

- $$PK_M$$ (Mallory) sends $$PK_A$$ (Alice) $1. Message signed with $$SK_M$$.

- $$PK_A$$ (Alice) sends $$PK_E$$ (Eve) $9. Message signed with $$SK_A$$.

Can you spot the invalid transaction? Although we don't have the balances of
each user, the transaction ledger gives us enough information to deduce every
user's balance at any given time. In this example, after the first three
transactions, Bob has $3, Mallory has $1, and Alice has $6. In the fourth
transaction, Alice is trying to spend $9 when she only has $6, so we know it
must be an invalid transaction. Because the ledger is trusted, it will reject
this invalid transaction.

At this point, we have created a functioning currency:

- Each person has a unique account, uniquely identified by public key.

- Users cannot impersonate other users, because each user can be validated by a
  secret signing key that only that user knows.

- Users can engage in a transaction by having the sender add their transaction
  to the ledger, with a signature on the transaction.

- Users cannot spend more than their current balance, because the trusted ledger
  is append-only, and everyone is able to calculate balances from the ledger.

The only remaining design element is creating a decentralized append-only
ledger, which we will discuss next.

{% comment %}
This last problem is sometimes called the \emph{double-spending} problem, and it
is notoriously difficult to solve in decentralized systems.
{% endcomment %}

## Hash chains

Recall that we need a public ledger that is append-only and immutable: everyone
can add entries to the ledger, but nobody can modify or delete existing entries.

To build this ledger, we will start with a _hash chain_. Suppose we have five
messages, $$m_1, m_2, \ldots, m_5$$ that we want to append to the ledger. The
resulting hash chain would look like this:

| Block 1 | Block 2 | Block 3 | Block 4 | Block 5 |
| $$m_1$$ | $$m_2, H(\text{Block 1})$$ | $$m_3, H(\text{Block 2})$$ | $$m_4, H(\text{Block 3})$$ | $$m_5, H(\text{Block 4})$$ |

Note that each block contains the hash of the previous block, which in turn
contains the hash of the previous block, etc. In other words, each time we
append a new message in a new block, the hash of the previous block contains a
digest of all the entries in the hash chain so far.

Another way to see this is to write out the hashes. For example,
$$\text{Block 4} = m_4, H(\text{Block 3}) = m_4, H(m_3, H(\text{Block 2})) = m_4, H(m_3, H(m_2, H(\text{Block 1}))) = m_4, H(m_3, H(m_2, H(m_1)))$$.
Note that Block 4 contains a digest of all the messages so far, namely $$m_1,
m_2, m_3, m_4$$.

{% comment %}

Below is from Nick in fa19 I think? -peyrin

## Editorial: Practical problems with Bitcoin

Bitcoin and other cryptocurrencies are very interesting from both a social and
technical viewpoint. Yet interesting does not necessarily mean \"good\". Bitcoin
technologically is a fairly simple idea: combining a public ledger with proof of
work. Yet it is a remarkably poor currency, a horrid store of value, and
surrounded by a community best described as "Bat Shit Insane".

There are also a lot of rather obscure details in Bitcoin itself, such as the
particular signature scheme and the use of double-hashing when a single hash
would suffice. We will skip over some of those details and present things at a
slightly higher level.

## The Bitcoin Public Ledger

A Bitcoin "wallet" does not actually store Bitcoin. Instead it simply contains a
set of one or more private keys. The hash of the corresponding public key (with
suitable checksumming to prevent some typos) is the corresponding "address"
where others can send payment. For example, this public address
1FuckBTCqwBQexxs9jiuWTiZeoKfSo9Vyi[^1] has a corresponding private key.

Spending Bitcoin is in many ways analogous to simply writing a check. If the
address 1FuckBTC\... wants to send .052 Bitcoin to 1Ross5\..., the person who
controls 1FuckBTC simply signs a message to the effect of "conduct this
payment". The resulting hash
<https://blockchain.info/tx/d6b24ab29fa8e8f2c43bb07a3437538507776a671d9301368b1a7a32107b7139?show_adv=true>d6b24ab29fa8e8f2c43bb07a3437538507776a671d9301368b1a7a32107b7139

[^1]:
    Created by Nick Weaver by simply generating a large number of random private
    keys until random luck generated one with the "FuckBTC" prefix

{% endcomment %}
