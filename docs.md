# b3t

b3t is a PVP + PVE betting protocol on Solana. Users bet against each
other directly, in private groups, or (soon) against prediction markets.
Stakes are held in escrow for the lifetime of a bet, and winnings are paid
out at settlement.

The protocol currently consists of the betting application and the $bet
token with revenue-share staking. Prediction markets are in development.

---

## Betting

### Creating a bet

A bet consists of a description, a winning condition for each side, a
stake amount, and a stake token. Stakes may be SOL or any token both
parties accept.

There are two bet types:

- **Direct challenge**: the creator designates an opponent by username.
  The bet activates when the opponent matches the stake.
- **Open challenge**: the bet is listed publicly in the arena, and any
  registered user may accept it. Acceptance is first come, first served.

The creator's stake enters escrow when the bet is created. A pending bet
can be cancelled by the creator at any time before acceptance, returning
the stake in full. A designated opponent may likewise decline it.

### Settlement styles

The settlement style is chosen at creation and determines how the winner
is decided:

| Style | Resolution |
|---|---|
| Either party | A single vote from either participant settles the bet immediately. Suitable when both parties trust each other. |
| Both agree | The bet settles when both participants vote for the same winner. |
| Judge | A designated third party decides the outcome with a single vote. The judge may be one of the participants or a neutral party. |

Open challenges are restricted to mutual agreement or a neutral judge,
and the judge of an open challenge may be neither the creator nor the
acceptor. This prevents a participant from adjudicating a bet against a
stranger.

### Judge consent

A judge assignment is not binding until the judge accepts the role.
Ordering is enforced: no counterparty can stake into a judged bet until
the judge has accepted, so funds are never locked behind a judge who has
not agreed to settle.

If a judge declines, the seat is vacated and the creator may assign a
replacement. Replacement is available any number of times while no judge
has accepted. Once a judge has accepted and the counterparty has staked,
the assignment is final: the judge recorded at the time of staking is the
judge who settles the bet.

### Refunds

Any active bet can be unwound with both stakes returned in full and no
fee charged:

| Style | Refund requirement |
|---|---|
| Either party | One request from either participant |
| Both agree | A request from each participant |
| Judge | A single request from the judge, or a request from each participant |

The joint participant path on judged bets guarantees that an unresponsive
judge can never strand escrowed funds.

### Judge earnings

The creator of a judged bet may attach a judge fee at creation: a fixed
amount in the bet's token, up to the size of one stake. The fee is paid
to the judge at settlement, out of the pot, after the protocol fee. The
winner receives the pot minus the protocol fee minus the judge fee, and
both amounts are visible to the counterparty before they stake.

A fee can only be attached when the judge is a neutral third party. A
bet judged by one of its participants cannot carry a fee, and a paid
judge seat can never be reassigned to a participant. Refunds pay no
judge fee: the fee is compensation for a ruling, and a refund returns
both stakes in full.

There is no minimum. A judge fee of zero is valid, and a judge may take
a seat purely for the love of the game.

### Open judge seats

A creator who does not have a judge in mind can leave the seat open at
creation. Open seats are claimable by whitelisted judges: accounts
vetted by the protocol, which is the sole authority for granting and
revoking whitelist status.

Claiming a seat appoints and accepts in a single step, so the bet
becomes stakeable immediately. The standard ordering still holds: no
counterparty can stake while the seat is vacant. A claimed judge may
step down while the bet is pending, which reopens the seat for the next
claimant, and the creator may still assign a specific judge to an open
seat at any point before one accepts.

Only bets created with an open seat are claimable. A bet whose named
judge declines does not become open to whitelisted judges. Combined
with judge fees, open seats function as a bounty board: a creator posts
the fee, and any whitelisted judge willing to rule for that price takes
the seat.

### Friends

Users add friends by username. A friend request requires acceptance by
the recipient and can be withdrawn by the sender while pending. The
recipient may accept, reject, or block. Bets between friends accumulate
on a shared head-to-head record.

### Groups

Groups are private, invite-only circles for pooled bets.

- Any member may create a group bet. Every entrant stakes an identical
  amount in the token the bet specifies.
- Payouts are winner-take-all or split across up to five ranked places in
  creator-defined proportions (for example 60/40 or 70/20/10).
- When the creator closes entries, the pot is locked and voting opens.
  Members vote on the complete finishing order, and the bet settles under
  the same three settlement styles as two-party bets.
- Payouts are claim-based: each winner collects their share at their
  convenience. If a bet is cancelled, every entrant reclaims their stake.

Membership requires consent on both sides: invitations must be accepted,
declining an invitation is always possible, and a group cannot be deleted
while any winnings remain unclaimed.

### Fees

The protocol charges **1% of winnings, at settlement only**. Refunds and
cancellations are free. Protocol fees fund the $bet revenue share
described below. If a bet carries a judge fee, it is deducted from the
pot at settlement alongside the protocol fee.

---

## The $bet token

### Launch mechanics

$bet launches through a buy-only bonding curve: a fixed allocation of
tokens sold at a price that increases with each purchase.

1. **Reserve.** While the sale is live, buyers spend SOL to purchase from
   the curve. Each purchase is a reservation, locked at the buyer's
   execution price.
2. **Sale end.** The sale ends when the raise completes at 5,480 SOL
   (~$400k), or earlier if closed by the team. The sale cannot reopen.
3. **Claim.** After the sale ends, each buyer claims their full
   reservation.

Sale parameters: 20% of supply (200,000,000 tokens) is allocated to the
sale, the price increases 6.76× from the opening price to the final fill,
and the implied fully-diluted valuation at the completed raise is
approximately **$5.2M**. Parameters may be adjusted before launch.

Proceeds from the raise seed a **Pump AMM pool** at the final curve
price. The pool is available for trading when the sale ends and is
deepened over time.

### Supply

Total supply is **1,000,000,000 $bet**, fixed at issuance: the mint
authority is revoked once the tokens are minted, so no further supply can
ever be created. No freeze authority exists.

| Allocation | Share | Tokens |
|---|---|---|
| Public sale | 20% | 200M |
| Team | 30% | 300M |
| Community distribution (future user rewards) | 15% | 150M |
| Treasury (integrations, incentives, runway) | 15% | 150M |
| Liquidity | 10% | 100M |
| Marketing | 10% | 100M |

### Revenue share

Holders lock $bet in a staking vault to earn a share of protocol
revenue. Distributions are paid in SOL and USDC from collected fees, not
from token emissions.

- Three lockup tiers: **1 month, 6 months, and 12 months**, earning
  **2%, 5%, and 8%** of protocol revenue respectively. 15% of all
  protocol revenue is distributed to stakers.
- Within a tier, each staker's share is proportional to stake size and
  stake duration.
- Positions continue earning after maturity until unstaked. Adding to a
  position restarts its lockup.
- Distributions accumulate in a per-staker vault and are withdrawable at
  any time.

### Buybacks

$bet buybacks funded by protocol revenue are planned. Exact sizing and
cadence will be determined within one month of launch.

---

## Prediction markets

Prediction markets are in development and will be added to the protocol
within one month of the PVP launch.

---

## TLDR

- Peer-to-peer and group betting in SOL or any agreed token, with
  escrowed stakes and settlement-time payouts.
- Settlement by mutual agreement or by a judge whose role requires
  explicit consent. Judges can earn a creator-set fee per ruling, and
  protocol-whitelisted judges can claim bets created with an open judge
  seat.
- Groups support pooled bets with ranked payouts across up to five
  places.
- Fees are 1% of winnings, charged only at settlement. $bet stakers
  receive 15% of all protocol revenue, weighted by lockup tier and stake.
- Fixed supply of 1B, with 20% sold on a rising bonding curve at an
  implied FDV of ~$5.2M at the completed raise (subject to adjustment
  before launch).
- Prediction markets follow within one month of launch.
