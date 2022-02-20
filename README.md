# GigaDAO: On-Chain Governance for SOL NFT Communities

## tldr; - 3 roles, each has limited power:
1) ***NFT Holders*** vote for board of directors each quarter.
2) ***Board of Directors*** appoints a CEO and votes on treasury withdrawal proposals.
3) ***CEO*** proposes treasury withdrawls to generate positive ROI for NFT Holders.

## Overview

GigaDAO (or gDAO) is a set of solana smart contracts that gives NFT holders control and clarity 
around the spending of treasury funds in a time efficient manner. 
Currently, most NFT “DAOs” have their funds fully controlled by a small number of team members. 
These are not really DAOs because the founder holds the private keys to the treasury and can rug at 
any time. Other DAO solutions require a majority of holders to vote for each withdrawal proposal. 
This is not practical when the CEO needs to act fast on investment opportunities. 
gDAO solves this by introducing a delegation vote, wherein NFT holders give their vote to a 
temporarily empowered “Council”. The Council consists of a group of eleven DAO members who have 
limited authority to approve treasury withdrawals during their tenure. 
The default tenure of a Counselor is one business quarter, and the default maximum withdrawal during 
that period is 25% of total DAO funds. 

## Architecture

### Staking Contract

1) Any NFT holder of a given collection can register with the DAO by staking one or more NFTs of that collection.
2) A holder's voting power is proportional to the number of NFTs they have staked, 1 NFT = 1 Vote.
3) To incentivize long term holders, a `vesting_period` (default = 1 month) can be configured such that the NFT's voting power vests in linearly over time. 
4) Upon registration, a holder can set `is_board_candidate = true` if they wish to be considered as a candidate for the Board of Directors.
4) Also upon registration, a holder can set `is_ceo_candidate = true` if they wish to be considered for the role of CEO.

### Holder Voting Contract

1) A new election for `num_board_members` (default = 11) is automatically triggered every `delgation_vote_frequency` (default = 3) months.
2) The election lasts for `delegation_vote_duration` (default = 7) days. During this time, holders cast their votes for any self-appointed candidates for the board of directors. The top `num_board_members` with the most votes are automatically marked `is_board_member = true` by the program.

### Board Voting Contract

1) CEO Election
   1) The Board of Directors is charged with electing a single CEO for the DAO.
   2) The elected CEO candidate must receive a supermajority (>50%) of votes from the BoD to be marked `is_ceo = true`.
   3) Only the CEO can generate on-chain budget proposals, and these proposals are the only way to withdraw funds from DAO treasury.
2) Budget Election
   1) The CEO can submit any number of withdraw proposals, but the total value of all proposals is programmatically limited to `max_quarterly_budget` (default = 25% of total treasury value). 
   2) This way, if the CEO and BoD are compromised, total risk to holders is limited.
   3) Budget proposals must pass with a supermajority of BoD votes in order to be processed. 
   4) All proposals and elections are on-chain so any holder can audit them. 


### Special elections
1) The Holder Voting Contract can be used to trigger the following special elections types:
   1) gDAO config settings adjustment - this triggers a general election to change basic DAO settings.
   2) Emergency Vote of No Confidence - A supermajority vote by holders can dissolve existing BoD.
2) The Board Voting Contract can be used to trigger the following special election types:
   1) Board Member Impeachment - A supermajority of BoD votes can impeach one board member and automatically promote the next highest candidate from the most recent general election.
   2) Emergency Vote of No Confidence - the BoD can dissolve itself - thus removing any personal liability associated with holding private keys linked to `is_board_member = true` status.
