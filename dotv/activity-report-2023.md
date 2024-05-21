# Activity Report 2023

## Introduction
This report summarizes the activities and financial performance of DotV OÜ during the fiscal year 2023.

For this purpose our financial performance detailing the contributing factors are layed out first.

Then the report dives deeper how the customer related business evolved during the year as well as how the internal projects evolved.

Last but not least the report details the strategic background and future outlook rounding everything up with the great conclusion of 2023.

## Executive Summary
Here in short the essence of the report.

### Financial Performance Summary
Our operations suffered a defecit of 3864€ where income of 11805€ could not cover the operational expenses of 15669€.
The total assets increased by 16235€ over the year.
At the same time the liabilities increased by 10000€.

This results in a net profit of 12 256 € despite the operational defecit.
Most of DotV's assets remain in crypto.

### Customer Service
Overall the direct service to our valued customers generated an income of 11805€. 

No new customer was succesfully aquired.

The year started with the failure to engage correctly with one specific new customer. The customer ultimativly left without any payment for the sevices. 

No further action targeted at expanding the customer base has been undertaken.

### Internal Projects
Progress has been made on following internal projects:
- AssetFlowControl System
- ThingyRPC 
- AuthMaster UI
- Secrets Cockpit
- Secret Manager Service
- Various OpenSource Packages: thingy-crypto-utils, thingy-crypto-node, etc
- Form-Mailer Service
- DotV Website
- QR-Code Cache PWA
- Data Server
- Mail Server
- Strunfun
- Thingy Toolset
- RandomRewards PWA

### Outlook
It is expected that at this stage there is no operational profit. 
This will continue in 2024, however the financial backbone evolved well which enables us to reduce liabilities in 2024.

Also the internal projects are evolving well thus we will be ready to shift focus on expanding the customerbase(+userbase) in 2024 again.

## Financial Performance
First we dive deeper into the financial performance of 2023.

See ... 

Total Assets (31.12.2022): 7 182 € (should be 7 984€) 
    - Investments: 7 088 € (should be 7 890 €)
    - LHV: 51 €
    - Wise: 43 €
Total Liabilities (31.12.2022): 31 300 €
Equity Net Assets (31.12.2022): -24 118 € (should be -23 316 €)

Total Revenue: 11 805,00 €
    - SustSol GmbH: 8 120,00€
    - Virtual Art Designory e.U.: 961,00€
    - David Höden / Höden . Jandl Schwimm- und
Sport GesbR: 2 724,00€

Total Expenses:  15 668,93 €
    - Tax: 3 839,73 €
    - Salary: 8 973,12 €
    - Domains/Hosting: 1 038,08€
    - Accounting: 1 818,00€

Total Assets (31.12.2023): 30 240 €
    - Investments: 29 599 €
    - LHV: 641 €
    - Wise: 0 €
Total Liabilities (31.12.2023): 41 300 €
Equity Net Assets (31.12.2023): -11 060€

Profit/Loss: + 13 058 € (should be 12 256 €)

### Liabilities
Throug the year DotV has taken additional loans from a very close person.
Specifically there were 2 loans one of 7 000 €  another one of 3 000 € making a total of 10 000 € of increased liabilities.


### Investment Performance
Most of DotVs' assets have been invested in crypto.
This is due to the initial funding of DotV - a loan in crypto from Lenard Frommelt, as well as the initial focus on the AssetFlowControl(AFC) System.

#### Testrun AFC
The latest Version of AFC had been tested with the company crypto assets from early December 2022 to late June 2023. 
Due to problems with the module which should track the balances and the profits, we only have estimated raw profits of about 200€/m.
However it is important to say that these profits now stated in € are to a part in crypto and not in €.

More exact data on the Performance of the Testrun was considered not being worth the effort.

#### Manual Trading
Manual trading has been done unsophisticatedly and rather sporadically/opportunistically.
Also here there were no exact data recorded.

#### General Performance
General and preprocessed data about the performance of the Portfolio we get from Koinly.
These are also the data which are used in the Tax report.

Portfolio Value 31.12.2022: 7 890 €
Portfolio Value 31.12.2023: 29 599 €

Unrealized gains: 10 522 €
Realized gains: 3 213 €

#### Additional Comments
It is important to add, that parts of the newly taken loans have been invested into the Portfolio.

Also the overall crypto-market evolved very well during 2023. 
Thus the increase in evaluation pales the estimated profits taken by both the AFC as well as the manual trades.
This can be seen to a part in the difference of Unrealized vs. Realized gains


## Customer Service

   - Describe the projects undertaken for customers:
     - List the customers served.
     - Outline the nature of the projects.
     - Mention any notable successes or challenges encountered.


## Internal Projects
Our internal projects received the major share of the attention.
Here we describe the achievements for each project we have been working on.

### Asset Flow Control System
The Asset Flow Control System (AFC System) is an automated service which ultimatively knows all details of all the users' accounts and wallets. Out of the infomration about the currently held assets, the targets and the chosen strategies combined with gathered intelligence about the markets, the automated service will engage in trading and facilitate transactions to take advantage of the market conditions. 

#### Status  Before
Implemented is v0.2 which consists of the core "trading-bot" plus the "trading-adapters" for kraken and binance and "observer-adapters" for kraken and binance. It is capable of trading asset pairs on kraken and binance based on given strategies.

Our the current sample strategy which is being tested is a very simple version taking advantage of volatility.

#### Progress in 2023
In _ of 2023 we started an evaluation period of the current AFCSystem.
The evaluation period as been stopped in _.

The result was: **Major issues in the stability and handling of the System.**
- the AFC Systems internal accounting became worthless over time (thus it has been ignored)
- trade deadlocks still happened -> action flow needs a redesign focusing on robustness 
- manual interference requires a UI 


In_ of 2023 we started testing a TradingView integration. This approach was to let Tradingview do the lions-share of market analysis to then send signals to a simple AFC System mockup.

The result was: **Works pretty well.**
- the signals arrive fast and the mockup reacted promtply
- we lack a sample strategy to reasonably employ these cabapilities
- the real AFC system requires a complete redesign to intelligently consider these signals 

#### ThingyRPC
The ThingyRPC will be the goto-way for communication of frontend to service as well as service to service.
Everything is a RPC and everything should simply be treated as a function call. Basically a modified jsonRPC with added authentication within the json of each request.

#### Progress in 2023
In March of 2023 we started the implementation.
The development of the ThingyRPC lead to various helper-packages as well as improvments of older packages like
- [secret-manager-crypto-utils](https://www.npmjs.com/package/secret-manager-crypto-utils)
- [thingy-session-utils](https://www.npmjs.com/package/thingy-session-utils)

In April of 2023 the generally usable [thingy-post-rpc-client](https://www.npmjs.com/package/thingy-post-rpc-client) package was published.

Later the general [thingy-crypto-node](https://www.npmjs.com/package/thingy-crypto-node) packages was created. 


#### Thingy Toolset
- Upgrade to fully support PWA development.


## Management
As a company where the only workforce, board-member and director is one person, management can operate absolute and direct. This comes with the highly welcome upside of execution speed and simplicity. 

The most significant decision also responsible for relatively poor financial performance and low expectations for the financial performance of 2024 is the focus on internal projects.

Internal projects are the bedrock of all further operations.
If additionals personal is employed they need to have the tools ready to be used. 
If further important costumers are to be aquired - with a solid toolset they can be served with the highest quality.

The target is to create revenue streams via published tools and platforms as this is more scalable and reliable than the service for costumers.

The service to costumers is a solid but unreliable source of income which should be just operationally profitable over the year while also inspiring but never limit the development of the internal projects.

### Financial Management

Also in the finances the director can operate absolute and direct.
Most assets are on the Kraken platform such that opportunities to buy or sell may be used at any time.

Also the AssetFlowControl System is our internal project build for managing assets. 
Therefore we use the company assets for live-testing of our system as well. Thus for evaluation periods some assets are automatically managed via the AssetFlowControl System.

During the 2023 overall we experienced an increase in the evaluation of crypto assets. While most of the time we kept on holding or buying more, which means low liquidity despite a decent amount of highly liquid assets.

Crypto is perfect for this. Our financial strategy is greedy and 




- Discuss how the company managed its finances:
- Address any deficits and the decision-making process behind borrowing money.
- Explain how reserves were utilized or invested, including performance in crypto.


Imporant is that all liabilities are interest-free loans from the director personally or his fiance/wife.

This means there is no real risk of a default on these loans.

Loans with interest from other potentially unforgiving sources are not an option for now and neither for 2024.

### Customer Relations
Customer relations are highly valued and invested in.

#### Loyal Costumers
All loyal costumers have direct contact with the director who is available almost 24/7.

While minor delays still occur - there has not been any negative feedback from the loyal costumers. 
The commitment stays strong to provide the best possible service to the loyal costumers.

#### Failed Customer
The unfortunate Situation with TOMO TEC was that the communication ran over a third person plus we assumed that the CEO with whom we were in contact did not want to be bothered and only is interested in the final results.
This lead to following problems:
- The customer felt neglected
- The customer had no understanding about specific problems which occured
- No estimate about required work-hours was shared with the customer
- The customer did not understand why so many work-hours were used
- The achievable result was not synchronized well with the customer
- Ultimatively the customer was not satisfied with the result and was unwilling to pay

##### Conclusions from this Incident
- The communication should go over the director
- The director should check sensibly what the customer expects and synchronize on these expectations
- It is better not to take on a customer with little compatibility

#### Customer Aquisition
In 2023 there has been no further attempts in aquiring new costumers.
For 2024 the target to aquire one more costumer has been set.


## Future Outlook:**
- Provide insights into the company's plans and prospects for the future:
- Discuss strategies for improving financial stability and profitability.
- Highlight opportunities for growth and development.



It is to expect that also 2024 will result in an operational deficit.
However in the best case we may still achieve a net profit with reduced liabilities.

The main Targets for 2024 for are:
- Publish first usable service
- Use the Void Mail Server to host costumer email addresses
- Aquire one new customer
- Reduce liabilities

For 2025 and later the prospect is to run operationally profitable with the new costumer plus added monitization of the published services.

## Conclusion:**
   - Summarize the key points of the report and reiterate the company's commitment to its mission and goals.

When filling in the details for each section, be sure to include relevant data, such as specific financial figures, project names, and key dates. Also, consider including any supporting documents or visuals, such as graphs or charts, to enhance understanding and transparency.