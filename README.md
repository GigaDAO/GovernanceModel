# GigaDAO: On-Chain Governance for SOL NFT Communities

## tldr; - 3 roles, each has limited power:
1) ***Holders*** vote for councillors each quarter.
2) ***Councillors*** appoints one High Councillor and vote on treasury withdrawal proposals.
3) ***High Councillor*** proposes treasury withdrawls to generate positive ROI for Holders.

## Overview

GigaDAO (or gDAO) is a set of solana smart contracts that gives NFT holders control and clarity 
around the spending of treasury funds in a time efficient manner. 
Currently, most NFT “DAOs” have their funds fully controlled by a small number of team members. 
These are not really DAOs because the founder holds the private keys to the treasury and can rug at 
any time. Other DAO solutions require a majority of holders to vote for each withdrawal proposal. 
This is not practical when the team needs to act fast on investment opportunities. 
gDAO solves this by introducing a delegation vote, wherein NFT holders give their vote to a 
temporarily empowered Council. The Council consists of a group of eleven DAO members who have 
limited authority to approve treasury withdrawals during their tenure. 
The default tenure of a Councillor is one business quarter, and the default maximum withdrawal during 
that period is 25% of total DAO funds. 

## Architecture

### Staking Contract

1) Any NFT holder of a given collection can register with the DAO by staking one or more NFTs of that collection.
2) A holder's voting power is proportional to the number of NFTs they have staked, 1 NFT = 1 Vote.
3) To incentivize long term holders, a `vesting_period` (default = 1 month) can be configured such that the NFT's voting power vests in linearly over time. 
4) Upon registration, a holder can set `is_councillor_candidate = true` if they wish to be considered as a candidate for the Council.
4) Also upon registration, a holder can set `is_high_councillor_candidate = true` if they wish to be considered for the role of High Councillor.

### Holder Voting Contract

1) A new election for `num_councillors` (default = 11) is automatically triggered every `delgation_vote_frequency` (default = 3) months.
2) The election lasts for `delegation_vote_duration` (default = 7) days. During this time, holders cast their votes for any self-appointed candidates for the council. The top `num_councillors` with the most votes are automatically marked `is_councillor = true` by the program.

### Council Voting Contract

1) High Councillor Election
   1) The Council is charged with electing a single High Councillor for the DAO.
   2) The elected High Councillor candidate must receive a supermajority (>50%) of votes from the Council to be marked `is_high_councillor = true`.
   3) Only the High Councillor can generate on-chain budget proposals, and these proposals are the only way to withdraw funds from DAO treasury.
2) Budget Election
   1) The High Councillor can submit any number of withdraw proposals, but the total value of all proposals is programmatically limited to `max_quarterly_budget` (default = 25% of total treasury value). 
   2) This way, if the High Councillor and Council are compromised, total risk to holders is limited.
   3) Budget proposals must pass with a supermajority of BoD votes in order to be processed. 
   4) All proposals and elections are on-chain so any holder can audit them. 


### Special elections
1) The Holder Voting Contract can be used to trigger the following special elections types:
   1) gDAO config settings adjustment - this triggers a general election to change basic DAO settings.
   2) Emergency Vote of No Confidence - A supermajority vote by holders can dissolve existing Council.
2) The Council Voting Contract can be used to trigger the following special election types:
   1) Council Member Impeachment - A supermajority of Council votes can impeach one council member and automatically promote the next highest candidate from the most recent general election.
   2) Emergency Vote of No Confidence - the Council can dissolve itself - thus removing any personal liability associated with holding private keys linked to `is_council_member = true` status.
