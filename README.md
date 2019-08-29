# Fixed supply crowdsale as Obyte Autonomous Agent
In 2017, Block.one launched a year-long EOS crowdsale on Ethereum with the twist - tokens didn't have the price, and you didn't know how many tokens you would get until the day was over. It has some other unconventional features like withdrawing invested ETH during the crowdsale, which gave the opportunity to re-invest investors money on crowdsale themselves and then sell those tokens on exchanges (ERC20 token became available to exchange on 5th day of the crowdsale). This Autonomous Agent (AA) for Obyte, written in Oscript, has none of that.

Another loophole, which some found on Ethereum was that, [you could drive the gas fee high on last minute of each day](https://coindecode.io/how-swim-made-thousands-gaming-the-eos-crowdsale/) and by doing that, keep the others from investing on last-minute if the price on exchanges was favorable for investing in crowdsale on that day. Obyte doesn't have that issue either because there are no blocks and no block producers who stand in your way to send your investment to AA.


## How to use
This AA has a minimal amount of parameters that the user needs to send to AA, basically `claim_period` parameter when investing and claiming from the single-address wallet. Source of the `fixed-supply-crowdsale.ojson` can be deployed using [Oscript editor for testnet](https://testnet.oscript.org). More about [AA on Obyte can be read from developer resources](https://developer.obyte.org/autonomous-agents).

### Starting the crowdsale
* The first bytes sent to stable/confirmed AA will also start the crowdsale with the period interval that was set in code when deploying.
* The same transaction will also define a new uncapped crowdsale token, which only this AA will be able to issue.
* The maximum total supply of crowdsale token is coded into the code, the formula is: `$max_claim_per_period * $max_crowdsale_periods`. The maximum total supply can be lower if some period will go without any investments (`total_raised_*` state variables are all the periods with investments). The circulation supply can be even smaller because not all will claim (`investor_*` state variables are all the investors who haven't claimed yet).
* If somebody has already deployed this exact code then change the number in `init` block and deploy a new one.

### Investing in the crowdsale
* Anyone sending bytes to AA will automatically become an investor with the address where the funds came from (don't send bytes straight from the exchange).
* AA will return a response message with the instructions on how to claim the crowdsale tokens.

### Claiming the crowdsale tokens
* Claiming is easy on the single-address wallet, just need to send `claim_period` number as data to AA, but it is safer to use the details from the response message, which will also show `claim_address` parameter that should be sent to AA.
* Anybody can pay the fees to claim the fees in case user spent all their last bytes for investment. Crowdsale tokens will be only sent to the same address where the investment was initially made from.
* Crowdsale tokens can be only claimed when the period the investor invested in is over.
* The amount of tokens the user will get when claiming depends how much other users invested in that exact same period, formula is: `round($investor_on_period / $total_raised_on_period * $max_claim_per_period)`.
* If nobody invested during some periods then the total supply of all the crowdsale tokens will be lower by the number of periods without investments.

### End of investment period or end of the crowdsale
* Each period changes automatically to next period without any input from anyone.
* Crowdsale also ends automatically without any input from anyone. All late payments will be bounced.

### Withdrawing for the founder
* Founder can invest own bytes in any on-going period, but they can withdraw user's bytes only when the crowdsale is over. This makes it impossible for them to re-invest the same user invested bytes in any of the rounds. That was the main criticism of EOS Crowdsale that it didn't have that limit and Block.one was withdrawing already on 5th day of the year-long crowdsale.


## Changelog

### 2019-08-28:
* Founder can withdraw user invested bytes (or other assets sent to AA) only after crowdsale is over.
* Founder can invest and claim with own bytes too, but not with user invested bytes because those are released only after crowdsale is over.
* Published this code on Bind Creative Github account, free for anyone to fork and improve.
* Added .gitattributes [JSON5](https://github.com/json5/json5) and .vscode Groovy syntax highlighting for *.ojson files.
* Added documentation and changelog to README file.

### 2019-08-23:
* Founder can withdraw other assets sent to AA too.
* Removed unnecessary `if` conditions on each `cases` objects because only the first case that matches the `if` condition is executed.
* Made PR to Obyte Oscript-editor repository to add this as a template, got declined.

### 2019-08-15:
* Changed state variable to `false` instead of `0` when investor claims their crowdsale, this will free up storage.
* Removed calculated `amount` from founder's withdraw, so founder could withdraw all bytes.

### 2019-08-14:
* Initial publish on Obyte Discord with EOS crowdsale rules.