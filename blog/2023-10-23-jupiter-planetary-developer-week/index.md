---
slug: jup-plantary-dev-update
title: "Jupiter Planetary Developer Week"
---

![Planetary](planetary.png)

## Introduction
Calling all Jupiter Space Catdets, welcome to the Jupiter Planetary Developer Week! 

We've been working really hard to bring you fantastic new features that will make it even easier for you to create amazing liquidity use cases on Solana. 

This is a whole week dedicated to Solana developers, where we will unveil major improvements to our tools, just in time for the Breakpoint conference. 

So, get ready to supercharge your **BUIDLing** journey with us!

## Day 1 - V6 Quoting API Official Release
Today, we are happy to announce the official release of our v6 API! This is DeFi’s most powerful and comprehensive liquidity API and will power the development of sophisticated use cases and complex DeFi processes on Solana.

This announcement comes on the heels of 2 months of relentless stability and feature-level improvements, helped immensely by key early adopters, including [Solend](https://solend.fi/), [Mango](https://mango.markets/), [Drift](https://drift.trade/), [Birdeye](https://birdeye.so/), [Hawksight](https://www.hawksight.co/), [Sphere](https://spherepay.co/), and [Kamino](https://kamino.finance/) who not just helped to iron out a lot of the kinks for any new infrastructure service, but also guided us towards specific improvements to make it possible for them to use Jupiter for their use cases.

In this article, we will go through key upgrades made for v6 to make building liquidity use cases much more powerful and easier:
- Upgrade to use the Metis Algo for higher liquidity to support key use cases
- Using shared intermediate token accounts for integration simplification and cost savings 
- Token ledger for increased swap success rates
- A brand new referral fees system

In addition, we will also show advanced usage patterns, examples of how our partners are using it. 

*This launch of the v6 API is part of a series of developer announcements we are rolling out to enhance the liquidity infrastructure landscape for Solana. Stay tuned!*


## Key features for V6 Quoting API

### Metis Algo As Backbone For v6 Routing 

Three months ago, we launched Metis on our homepage. It's an innovative new algorithm with real time streaming and discovery, with the routing engine also completely rewritten in Rust. This heavily modified variant of the Bellman-Ford algorithm incorporates key features such as incremental route building, combined route generation and quoting, and it's also future-proofed for much better scalability as we add more DEXes and tokens.

The previous version of the API utilized an older version of the algorithm that had significant limitations. Most notably, it did not provide efficient routing for large trades, which constrained its applicability for major use cases such as margin trading and liquidations. Moreover, it employed the former JavaScript engine, resulting in inconsistent API performance.

![Planetary2](planetary2.jpg)

With the v6 API now using Metis, all partners can enjoy a far higher level of liquidity for key use cases. For example, Solend which is using it for margin trading, where the trading amount is often much larger and requires deeper liquidity. 

On the backend, we are constantly upgrading the API and making incremental improvements to deliver an easier, smoother and more efficient liquidity infrastructure for our partners.  

### Shared Intermediate Token Accounts For Integration Simplification and Cost Savings
The idea of Shared Intermediate Token Accounts came about because there was an increasing number of intermediate token accounts to be created in order for a swap to be successful, which increased integration complexity and cost for users that needed to be addressed.

Previously: 
- Integrators needed to create every single intermediate token account before integrating and obtaining quote routes, to make sure that the swaps were executed without any disruption. Any token accounts that were missed out would lead to swap errors.

- In Solana, an account rent fee is incurred for every Solana account to store data on the blockchain. With a large number of intermediate token accounts to be created per protocol, users incur a sizable amount of rent. 

With the Shared Intermediate Token Accounts, Jupiter creates and maintains a set of Associated Token Account *(ATA)* contracts for intermediate token accounts. These shared accounts are global and shared among all protocols and partners integrated with Jupiter. Integrators now only need to ensure they have the final output token account to receive tokens for every swap. 

On top of this, OpenBook Open Orders accounts are no longer required as well, because all orders accounts are also globally initialized and shared amongst integrators.

For instance, when swapping from Token A → Token B → Token C, instead of having to create accounts for Token B and Token C, integrators will only need to set up for the final out token *(Token C in this case)*. Any intermediate tokens in between, regardless of the number of token hops, are automatically initialized and managed by Jupiter’s pre-established shared token accounts. 

Creating individual token ATA accounts incurs account rent fees, which accumulate over time due to the growing number of intermediate tokens. With fewer intermediate token accounts required, users benefit from not having to incur account rent fees for maintaining their individual token ATA accounts with each protocol they swap on.

We also see a much simpler integration process for partners and integrators, as the need to create and manage numerous intermediate token ATA accounts via route maps is eliminated.

Integrators can also better compose swap instructions that fit within a single transaction size limit, as the number of accounts to pass through in each instruction is streamlined with these shared ATA accounts.

### Token Ledger For Increased Swap Success Rates

It is very important to have a good swap success rate for a great experience for users as well as integrators. Token Ledger was developed because the common way of relying on simulation to facilitate swaps is not always reliable and can lead to swap failures especially during times of market volatility. A method that is based on actual amounts transacted, and combined into a single transaction is greatly needed, leading to the birth of The Token Ledger. 

The Token Ledger is a collection of three instructions *(Set Token Ledger, Send Instruction, Swap Instruction)* that consolidate withdrawal and swap instructions into a single transaction. Integrators/partners can now effortlessly instruct a swap to be executed based on the actual sent amount, after taking into account fees and slippages. 

Before the introduction of the Token Ledger, swap instructions relied on a simulation-driven approach to approximate the amount deducted from a user's wallet to facilitate the swap. This estimation approach often ended up with discrepancies between the estimated and actual amounts, especially during periods of market volatility with high slippages, leading to swap failures.

Consider this scenario where a user intends to withdraw 1,000 USDC from Meteora’s USDC vault into wBTC. Note that users hold vUSDC LP tokens in Meteora vaults. 

1. In the first instruction, the Token Ledger will verify and record the user’s initial USDC balance within their wallet. For the purpose of this example, let’s assume the initial balance is 100 USDC. 
2. In the second instruction, an amount equivalent to 1,000 USDC in vUSDC is withdrawn from Meteora vaults into USDC, and we examine the user’s balance. Due to factors like slippage, the USDC balance is found to be 1090 USDC. 
3. For the third instruction, subtracting the initial 100 USDC from the current 1090 USDC balance, it’s evident that the user has effectively withdrawn 990 USDC, which will then be utilized to swap for wBTC as the final output token.

Without the Token Ledger, the previous swap method relied on estimated amounts, which could fluctuate due to market volatility and performance variations, frequently resulting in suboptimal user experiences.

With the Token Ledger, integrators can issue swap instructions based on actual amounts that have taken into account slippage and related fees. This eliminates the need for simulation and significantly increases the success rate of swaps, all within a single transaction.

The Token Ledger caters to a wide range of use cases, which include but are not limited to:

- Withdrawing assets from a protocol to any desired token, such as converting to USDC from a wBTC vault.
- Supporting protocols that transition positions to a different token from their original position, as seen in Perpetuals.
- Facilitating the exchange of NFTs for tokens other than SOL.

Read more about the Token Ledger: 
https://station.jup.ag/docs/v6-beta/swap-api#using-token-ledger-instruction 

### Referral Fees Program to Simplify Fee Collection
In our pursuit of making life easier for our partners, we've streamlined the process of charging referral fees with the new referral fees program.

In the past, setting up referral fees for our partners was not straightforward. Distributing referral fees to Referrer A and Jupiter required adding two more accounts to the instruction. It was also challenging to accommodate varying percentages of referral fees for different partners, needing to pass more accounts into the instruction to calculate these fees. 

The motivation behind building the referral fees system was to automate the process of calculating all these fees, while providing a user-friendly interface to set up referral accounts and claim the referral fees collected. 

#### Referral Fees System Features
- Set up referral accounts with Jupiter via a simple interface requiring only a few clicks.
- Only 1 single referral account is needed across the different Jupiter products. 
- Checking on the total fees collected across the token accounts with an easy button to claim them

The partner journey is now hyper easy - They head over to Jupiter, create a referral account, and then plug the referral account parameters into their integration. It's that simple!

Give our dashboard a spin here: https://referral.jup.ag

Read here on how to add your platform fees to Jupiter Swap : https://station.jup.ag/docs/v6-beta/adding-fees

More info on Jupiter Referral Program: https://station.jup.ag/docs/additional-topics/referral-program 

![Referral](referral1.jpg)


## Performing Advanced Swaps with Jupiter v6

### Cross Program Invocation (CPI) & Flash-fill
The Cross Program Invocation (CPI) and Flash-fill are two approaches to integrate Jupiter swap with your protocol. In Jupiter v6 API, we have made CPI calls much easier to use by turning transaction payload into instructions for API users.

#### CPI Approach
To better understand how to swap via CPI, let us walk through an example of utilizing Jupiter Swap via CPI to swap from any tokens to SOL even if the user has insufficient SOL.

For CPI to work, the transaction will be composed of these instructions:
1. Borrow enough SOL from the program to open a wSOL account that the program owns.
2. Swap X token from the user to wSOL on Jupiter via CPI.
3. Close the wSOL account and send it to the program.
4. The program then transfers the SOL back to the user.


Read more here: https://station.jup.ag/docs/v6-beta/cpi

The CPI approach is not without its limitations - because of Solana’s transaction limit of 1232 byte size, lookup tables cannot be used within a CPI call, swaps via CPI can fail at runtime since Jupiter routes may involve multiple DEXes in order to reduce price impact. Instead, we recommend taking the “flash-fil” approach to utilizing Jupiter Swap. 

#### Flash-Fill Approach
The “Flash-fill” approach is a way to integrate your program with Jupiter swap without the limitations of CPI. Flash-filling allows the use of Versioned Transactions in combination with Address Lookup Tables to include more accounts per transaction while keeping within the 1232 bytes limit. 

To understand the Flash-Fill approach, we will walk through the same example of utilizing Jupiter swap via Flash-Fill to swap from any token to SOL even if the user has insufficient SOL. Note that Flash-Fill can facilitate swaps from any token to any token, not limited to SOL.

For Flash Fill to work, the transaction will be composed of these instructions:
1. Borrow enough SOL from the program to open a wSOL account that the program owns.
2. Create the wSOL account for the borrower.
3. Swap X token to wSOL
4. Close the wSOL account and send it to the borrower.
5. Repay the SOL for opening the wSOL account back to this program.

This approach entirely bypasses the need for CPI calls, effectively sidestepping the constraints imposed by CPI.

To delve deeper into these two approaches, check out our documentation here: https://station.jup.ag/docs/v6-beta/flash-fill

## Partners Using v6 API
A big shout out to our key early adopters for running our v6 API and helping us improve our stability and features. Check out how they are using Jupiter to power their use cases.
- [Drift](https://drift.trade/) leveraged swaps utilise the power of “flash loans” to allow traders to increase their spot buying or selling potential by up to 5x. These leveraged swaps are powered by Jupiter, to find the best price routes and get access to deep liquidity across Solana.
- [Solend](https://solend.fi/) Margin Trading is an extension of their lending where it combines flash loans, swapping, and depositing the proceeds back into Solend all in a single transaction. The trading view features a swap UI powered by Jupiter.
- [Birdeye](https://birdeye.so/) is an on-chain trading data aggregation platform that utilize Jupiter Swap for all SPL tokens including the latest Token2022. With the latest v6 upgrade, it offers more improved speed that enhances trading experience on Birdeye.
- [Hawksight](https://www.hawksight.co/) integrated Jupiter v6 for their auto-swap deposits, making it super easy for users to maximise yield through auto-compounding and auto-rebalancing their LP positions efficiently. 
- [Ultimate Wallet](https://ultimate.app/) integrated Jupiter into their native in-wallet swap. With the upgrade from v4 to v6, Token2022 is fully supported and the latest Metis algorithm expanded the price routes, enabling their users to swap over the widest range of tokens and access even better price quotes for their users.
- [Sphere](https://spherepay.co/) is a payments platform and API for digital currencies, where businesses can start accepting card, ACH, wire, stablecoins, and other digital currencies in a single universal checkout page. Jupiter provides the swap infrastructure for the built-in token swap.
- [Sollinked](https://sollinked.com/) is a social app designed for gated communities, offering a platform for newsletters or "paid" email inboxes that can be prioritized through incentives. Sollinked utilizes Jupiter to facilitate payments for emails and reservations using all SPL tokens. 

## Day 1 Ending Notes
**Jupiter’s goal is to provide the best possible liquidity infrastructure for Solana.**

With this major v6 upgrade, we are confident we have taken a major step in this direction. As always, we would love feedback and your thoughts on how we can improve, please join our feedback conversations on [Discord](https://discord.gg/jup)!


<!-- **Stay tuned for Day 2 of the Jupiter Planetary Developer Week where we are going to talk about Jupiter and payments!** -->

---

![PlanetaryDay2](planetaryday2.png)

## Day 2 - Jupiter for Payments v2
Today, we are thrilled to dive into the world of Jupiter for payments, where we will introduce you to the upgraded ExactOut API. Join us as we delve into the improvements we have made to our ExactOut API, and how we have been facilitating online and offline payments in recent months. 

A big shout out to [**Sollinked**](https://sollinked.com/), [**CandyPay**](https://candypay.fun/), [**Sphere**](https://spherepay.co/) and [**Helio**](https://www.hel.io/) for integrating Jupiter swap into their payment systems. These are super exciting times as we break down the barriers separating the world of crypto from traditional finance.

So, without further ado, let's get started!

## ExactOut v2 API to Power Payments
ExactOut is an important feature, particularly in supporting payment use cases within any ecosystem. Jupiter's ExactOut API allows users to specify the precise amount of output tokens they require, with the input token amount being calculated accordingly. For instance, with ExactOut integrated, a protocol’s user can indicate they wish to receive exactly 100 USDC in exchange for SOL via Jupiter, instead of having to manually calculate and input the amount of SOL required to swap for an approximate amount of 100 USDC. 

In the initial release of ExactOut v1, we could only support direct routes, limiting token selection and routing options that can be used. However, in Jupiter v6 API, our program has been updated to conduct on-chain calculations, thus enabling multi-hop routes for ExactOut v2. 

This upgrade significantly broadens the range of supported routes from around 20 to over 400 trading pairs, enhancing route options and price selections. With 20 times more available price routes, aggregated across our partner platforms like Raydium and Orca, users can expect considerably better prices for their ExactOut swaps.


### How ExactOut is used
ExactOut is particularly useful in scenarios involving the purchase of NFTs, allowing users to make payments with any tokens they possess. To illustrate, let's consider a situation where an NFT Marketplace has integrated ExactOut on its platform. You wish to buy your favorite NFT which is priced at 40 SOL, but most of your liquid assets are in BONK. Without ExactOut, you would need to estimate the amount of BONK required to obtain 40 SOL and manually swap on an external platform to convert BONK into SOL.

The good news is that since the NFT Marketplace has integrated ExactOut into its interface, you can simply purchase the 40 SOL NFT and Jupiter swap would automatically calculate the most optimal amount of BONK required to purchase the 40 SOL NFT. This is achieved in a single click, eliminating the need to leave the site or perform additional calculations, while ensuring that you receive the best possible price through Jupiter.
This is extremely important to Solana as payments are a crucial part of bridging crypto with real-life applications, ultimately contributing to the mainstream adoption of the Solana ecosystem.

Read more about ExactOut here: https://station.jup.ag/docs/v6-beta/payments-api 

### ExactOut Showcase #1 - Sollinked

Sollinked is a social app designed for gated communities, offering a platform for newsletters or "paid" email inboxes that can be prioritized through incentives. Sollinked utilizes Jupiter to facilitate payments for emails and reservations using all SPL tokens.

See how Sollinked users make payments with various SPL tokens to bump up their newsletters and emails, even though payments are primarily denominated in USDC. This is possible through the integration with Jupiter ExactOut.

![Sollinked1](sollinked1.gif)

![Sollinked2](sollinked2.gif)


### ExactOut Showcase #2 - CandyPay Coffee

Candy Pay has integrated Jupiter to power built-in swap transactions to facilitate payments in SPL tokens. Users can now make purchases with any token they have in their wallets. 

Check out this demo of a user buying a cup of coffee priced in USD with their SAMO tokens. How cool is that!

https://twitter.com/JupiterExchange/status/1638799925348794369 


### ExactOut Showcase # 3 - Helio

Helio, the leading payments app for Solana, powers sales for +3,000 creators & merchants who sell NFTs, digital content, e-commerce and more with the power of blockchain payments. Helio partnered with Jupiter to power seamless token swaps inside the Helio checkout flow. Buyers can spend SOL or any token, while merchants always receive USDC or their asset of choice.

`"The future of commerce is based on lightning fast blockchain payments and one of the key advantages is that buyers can spend any token while merchants instantly receive their own currency of choice. Jupiter has abstracted the complexity of a DEX and given us a few simple lines of code to help us deliver on this promise"` **CTO - Jim Walker**

![Helio](heliodemo.gif)

### ExactOut Showcase # 4 - Sphere Pay
Sphere is an end-to-end payments API for digital currencies, offering on-ramp, multi-chain and fiat payments, to off-ramp in a single integration.

Sphere uses Jupiter to enable “universal settlement”, where customers can pay in whichever currency they want, and merchants will receive any currency of their choice — with Jupiter handling the ExactOut swap behind the scenes at the best possible rates.

Here is an example of how Sphere users can select to pay in any currency of their choice, with merchants receiving any currency they wish. Thanks to v6, these swaps are blazing fast, and shared ATAs and versioned transactions/lookup tables allow all of this to be bundled into a single transaction.

![Sphere](spheredemo.gif)

### Ending Words

With Jupiter providing the essential infrastructure support for payments, we see Jupiter playing an important role in helping support the payment and fees infrastructure of Solana. 

If you have any feedback and product improvements, do feel free to swing by and share them on our [**Discord**](https://discord.gg/jup)!

**Stay tuned for Day 3 of the Jupiter Planetary Developer Week where we are going to talk about the much anticipated Terminal v2!**