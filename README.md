# BenchGov - The Governance For The Bench Network

## BenchGov Introduction 

This paper specifies the Governance module of the Bench Network or anything forked from the Bench Network. 
The Governance enables Bench Network's MultiChain to support a governance system on both the `RootChain`, `SideChains` and `dappChains`
integrated with the Bench Network. 

In this system, BEN (BenchCoin) holders or the native staking coin of the `SideChain` or `dappChain` in particular
can vote on what we call `Motions`, on a 1 native staking coin to 1 vote scale. 

## Bench Network Definitions
* Bench Network - DPOS MultiChain, Powered By BenchCore.
* RootChain - First blockchain added to the MultiChain. In Bench Network's MultiChain, BenchChain is the `RootChain`
* BEN / BenchCoin - Native Coin of the Bench Network's `RootChain`
* BEN holder - an entity that holds some amount of BenchCoins
* Candidate - a BEN holder that is actively involved in the BenchChain
  blockchain protocol running a BenchChain Full Node and is competing with other candidates to be elected as a
  Rocketeer.
* Rocketeer - a candidate that is currently selected among a set of candidates
  to be able to sign protocol messages in the BenchChain logic protocol
* Delegator - an BEN holder that has insured some of its BenchCoins by delegating
  them to a Rocketeer (or a candidate)
* Insuring BenchCoins - a process of locking BenchCoins in a insure deposit (putting BenchCoins
  under protocol control). BenchCoins are always insured through a Rocketeer (or
  candidate) process. Insured BenchCoins can be slashed (burned) in case a Rocketeer
  process misbehaves (does not behave according to the protocol specification).
  BEN holders can regain access to their insured BenchCoins if they have not been
  slashed by waiting an Uninsuring period.
* Uninsuring period - a period of time after which BEN holder gains access to
  its insured BenchCoins (they can be withdrawn to a user account) or they can be
  re-delegated.
* Inflationary provisions - inflation is the process of increasing the BEN supply.
  BenchCoins are periodically created on the Bench Network and issued to insured BEN holders.
  The goal of inflation is to incentize most of the BenchCoins in existence to be insured.
* Transaction fees - transaction fee is a fee that is included in a Cosmsos Hub
  transaction. The fees are collected by the current rocketeer set and
  distributed among Rocketeers and delegators in proportion to their insured
  BEN share.
* Commission fee - a fee taken from the transaction fees by a rocketeer for
  their service
  

## Currently Implemented Features


Next is a list of features the Bench Governance currently supports:

- **Submitting Motions:** Users can submit motions with a deposit. Once the minimum deposit is reached, motion enters voting period
- **Vote:** Participants can vote on motions that reached MinDeposit
- **Inheritance and penalties:** Delegators inherit their rocketeer's vote if they don't vote themselves. If rocketeers do not vote, they get partially slashed.
- **Signal and switch:** If a motion of type `SoftwareUpgradeMotion` is accepted, `Rocketeers` can signal it and switch once enough rocketeers have signalled.
- **Claiming deposit:** Users that made a deposit on Motions within a chain can recover their deposits if the motion was accepted OR if the motion in question never entered voting period.

Features that may be added in the future are described in [Future improvements](future_improvements.md)

This particular Governance will be used in the Bench Network, which will become the first Chain and therefore the `RootChain`
within the Bench Network.


## Table of Contents

The following specification uses *BEN* (BenchCoin) as the native staking token. The module can be adapted to any Proof-Of-Stake blockchain by replacing *BEN* (BenchCoin) with the native staking token of the chain.

1.  **[Design overview](#)**
2.  **Implementation**
    1. **[State](#)**
        1.  Procedures
        2.  Motions
        3.  Motion Processing Queue
    2. **[Transactions](#)**
        1.  Motion Submission
        2.  Deposit
        3.  Claim Deposit
        4.  Vote
3.  **[Future Implementations](#)**

