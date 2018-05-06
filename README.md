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


## Bench Governance Overview

The governance process is divided in a few steps that are outlined below:

- **Motion Filing:** Motion is filed on the `RootChain`, `SideChain` or `dappChain` along with a deposit. 
- **Motion Voting:** Once deposit reaches a certain value (`MinDeposit`), motion is confirmed and vote opens. Insured BEN holders can then send `NtxGovVote` transactions to vote on the motion.
- Software Upgrade Motions:
  -- **Signal:** Rocketeers start signaling that they are ready to switch to the new version
  -- **Switch:** Once more than 2/3rd Rocketeers have signaled their readiness to switch, their software automatically flips to the version from the Motion regarding the software upgrade.
  
## Motion Filing

### Your Right To File A Motion

Any BEN holder, whether insured or uninsured, can file motions by sending a `NtxMotion` transaction. Once a motion is filed, it is identified by its unique `motionID`.

### Preventing Motion Spamming

To prevent spam on the Bench Network in regards to Motion filings, motions must be filed along with a  `motionDeposit`. `motionDeposits` must be paid in with the BEN denomination. Other coins available on the `RootChain`, `SideChains` or `dappChains` can not be used to cover a `motionDeposit`. 

Other BEN holders can increase the Motion's deposit in BEN so that it works out to look likes this:  `0 < deposit < MinDeposit`, by sending a `NtxGovDeposit` transaction. Once the Motion's deposit reaches `MinDeposit`, it will finally enter its voting period, where the filed Motion can be voted on by members of the Bench Network community. 

### Refunded Motion Filing Deposit

There are two instances where BEN holders that deposited BEN on pending Motions in the system, can reclaim their Motion deposit:
- If the Motion is accepted
- If the Motion's deposit does not reach `MinDeposit` for a period longer than `MinDepositPeriod`. The default value for `MinDepositPeriod`is 60 days.

If a Motion has no deposits for 60 days, it's closed permanently and would have to be reposted to start the  `MinDepositPeriod`over.

In such instances, BEN holders that deposited can send a `NtxGovClaimDeposit` transaction to retrieve their share of the deposit.

### Motion Types and Categories 

In the initial version of the governance module, there are two types of proposal:
- `PlainTextProposal`. All the proposals that do not involve a modification of the source code go under this type. For example, an opinion poll would use a proposal of type `PlainTextProposal`
- `SoftwareUpgradeProposal`. If accepted, validators are expected to update their software in accordance with the proposal. They must do so by following a 2-steps process described in the [Software Upgrade](#software-upgrade) section below. Software upgrade roadmap may be discussed and agreed on via `PlainTextProposals`, but actual software upgrades must be performed via `SoftwareUpgradeProposals`.

There are two categories of proposal:
- `Regular`
- `Emergency`

Both are essentially identical other than `Urgent` motions having the ability to be accepted faster, as long as the Emergency conditions of an Emergency proposal are met. 

## Vote

### Participants

*Participants* are users that have the right to vote. On the Bench Network, participants are insured BEN holders. Uninsured BEN holders and other users do not get the right to participate in the Bench governance. However, they can file Motions and make Motion deposits.

### Voting Span

Once a Motion reaches `MinDeposit`, it immediately enters what's is called a `Voting Span`. We define `Voting Span` as the interval between the moment the vote around a Motion opens and the moment the vote around a Motion closes. `Voting Span` should always be shorter than `Uninsured Span` to prevent double voting. The default value for `Voting Span` when filing a Motion is 2 weeks.

### How You Vote

When casting a vote on a Motion, a participant has four options to choose from, although other options can be set by the person or network filing the Motion. 

The default voting options includes the following options: 
- `Yes`
- `No`
- `Veto` 
- `Abstain` 
- `NotEmergency`

Explanations of voting options: 

- `Veto` counts as `No` but also lets the network know that voter is Vetoing the Motion and will not accept the outcome of the Motion's vote. 
- `Abstain` doesn't count as a `Yes` or `No` but does allow voters to abstain from voting on a Motion, while also accepting the outcome of the Motion's vote. 
- `NotEmergency` only appears on for Emergency Motions and allows voters to proclaim a Motion as non-urgent. 

### Minimum Vote 

The ability for us to use Minimum Voting rules for filed Motions, is still in the works but apart of our Governance model. Our 2nd software upgrade for Bench Network will introduce a way for a minimum percentage of `Voting Fuel`, that's required for the result of a Motion's vote to considered valid by the network. 

Currently, participation is ensured via the combination of inheritance and a Rocketeer's punishment for his/her failure to vote on a Motion. 

