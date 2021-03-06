Intro
--
GoodDollar is an economic framework with a cryptocurrency based on Universal Basic Income.

Developer Resources
--
- [Docs & Guides](https://docs.gooddollar.org)
  - JSDocs and some guides about the architecture
  
- [Dev chat](https://gitter.im/GoodDollar)
  - Ask us anything
  
- [Quick start dev env](https://github.com/GoodDollar/GoodBootstrap)
  Installs and setup the repos GoodDAPP, GoodServer, GoodContracts and GoodGun
- [GunDB](https://gun.eco)/[GunDB chat](https://gitter.im/amark/gun)  
  
 Challenges
 --
 
 - [GoodIdentity](#GoodIdentity)
 - [GoodDAO](#GoodDAO)
 - [GoodReserve](#GoodReserve)
 - [GoodInfrastructure](#GoodInfrastracture)
 
 
 ### GoodIdentity
 Implement an SDK and extend GoodDAPP to enable "Login with GoodDollar".
 Every GoodDollar citizen has a verified "unique and live" self soveriegn identity (profile).
 Profile details are stored encrypted with the user private key over gundb using gun encryption and user management library SEA.
 You should implement a system that would enable a 3rd party to request the user details in order for him to login to that 3rd
 party website/service.
 
 An example flow:
 - user clicks on a "Login with GoodDollar" button
 - A QR code and/or a link is displayed to the user
 - The user scans the QR code with his GoodDAPP
 - The user sees the request details, the requester details and what details does the requester require
 - The user then chooses which data to share
 - GoodDAPP sends the data to the requester
 
Technical Requirements
- Javascript snippet for generating the Login button and QR/Link
- QR/Link should include the 3rd party GoodDollar account wallet address/[gundb profile](https://github.com/GoodDollar/GoodDAPP/tree/e17074702ef6fdbb49430cd02002fcd1c453bc8b/src/lib/gundb)
   - When an account is whitelisted his blockchain address+profile public key are saved to the [identity smart contract](https://github.com/GoodDollar/GoodContracts/blob/alphav2/contracts/Identity.sol)
- GoodDAPP verifies details from link/QR [GoodDAPP QR usage](https://github.com/GoodDollar/GoodDAPP/blob/e17074702ef6fdbb49430cd02002fcd1c453bc8b/src/components/dashboard/SendByQR.web.js)
    - login request came from a whitelisted account
    - login request website/details were signed by the requester profile public key. If link was used its possible to compare the referer website to the details
 - GoodDAPP shows user a [modal](https://github.com/GoodDollar/GoodDAPP/blob/4af2b2d3bc5e4ce2d2ffc4d8b9e1f52367165578/src/lib/undux/utils/dialog.js) asking which details to send
     - The requester details are shown
     - The request specific details are shown
     - wallet address, profile public key, fullname, image, username are always sent
     - email/mobile are optional and can be enabled/disabled by user
 - GoodDAPP needs to prove to requester he's the owner of the profile public key
    - need some kind of handshake between requester server and GoodDAPP. maybe server sends a nonce that GoodDAPP signs with public key
    - Communication can be done via regular https end points or via gundb (websockets based)
 - requester server redirects user
     - in order for requester page showing QR to refresh, there probably should be a unique identifier generated by the server that is also
   send via the QR. this identifier could be the unique channel over gundb that they communicate over. in any case communication should be
   encrypted with public keys.
      - The QR page could be also listening on that gundb channel to know when user was logged in
 - optional: requester is added with permissions to read the user profile fields that were enabled using SEA .grant method
   - requester can always be updated when details change
   - requester doesnt need to keep any data at all since he can always access the details
   - user can revoke access
 
 
 ## GoodDAO
 Implement curation markets using [DAOStack's tools](https://github.com/daostack/DAOstack-Hackers-Kit) and [holographic consensus](https://medium.com/daostack/holographic-consensus-part-1-116a73ba1e1c).
 The GoodDollar citizens (DAO) would need to make different decisions and also manage common pool resources.
 Two examples for such resources are:
 1. The GoodReserve - a smart contract which acts as a reserve holding different erc20 tokens donated
 or exchanged for GoodDollars. How would the citizens allocate these funds? which projects should the citizens invest in?
 2. The GoodFeed - the feed every user sees when he opens his wallet, this a scarce and valuable resource for passing information 
 to the citizens of GoodDollar. How would the citizens decide which news and announcements are worthy to be displayed to everyone?
 
 Technical Requirements
 - A script to deploy DAOStack's smart contracts on fusenet.io, with the GoodDollar as token for making stakes.
 - Choose a topic for curation, could be one of the two examples above or anything else you think might be important
 - UI for one of or all:
   - submitting proposals, preferably through the GoodDAPP
   - voting on proposals, preferably through the GoodDAPP
   - staking on proposals, preferably through the GoodDAPP
   - list of winning proposals in GoodDAPP
   
 ## GoodReserve
 Implement the reserve smart contract. The reserve has three main functions
 - Accept donations in Ether (or other ERC20 tokens)
 - Sell/Buy GoodDollars for Ether using bonding curves
 - Manage the foreign currency reserves for the benefit of the GoodDollar citizens
 
 Technical Requirements
 - Read our economic paper draft, specifically chapter 5.
 - Implement a smart contract with the above 3 main functionalities and key things in mind
     - How would you try to prevent GoodDollar from being a speculative currency through market making?
     - GDX, a token that can give access to the reserve, not every one can exchange their GoodDollars
     - Citizen Dividend - When can you give GDX as a dividend part of the UBI
 
 ## GoodInfrastracture
 Implement a SAAS based on exisiting open source software that provides services only for GoodDollars
 In order to create value for GoodDollars we would like to provide digital goods and services to our citizens.
 We believe using open source software and the current cheap pricing of hosting services we could provide real added value for our citizens
 and demand for GoodDollars.
 Two examples for such services:
 1. Wordpress hosting
 2. MongoDB shared hosting
 
 Technical Requirements
 - A UI for paying with GoodDollars for the service
 - A containarized SAAS platform that can open user accounts for users that payed with GoodDollars.
   - Each user user would have his own dashboard/control panel to manage and access his service
   - Service would remind users to pay for next term
   - Service is denied if payment wasn't renewed
