# Lazy Trader

A trading strategy shared by [kjmsb2](https://www.reddit.com/user/kjmsb2) on [this post](https://www.reddit.com/r/Forex/comments/b1tuhb/a_lazy_way_to_trade_forex/).

------------

I have been using this method for a while with pretty consistent results.

I use 3 pairs on the daily charts (chart time frame is actually irrelevant): EURJPY, EURUSD, USDJPY. This gives me a hedge between the 3 positions.

At the opening hour of Forex trading (5pm EST Sunday for me) I place both a pending buy-stop and sell-stop order 30 pips away from the opening price for each pair.

I place stop-loss at 50 pips and take-profit at 150 pips for each. I also instruct MT4 to use a trailing stop of 500 points (50 pips) once the position moves in to profit.

That's it.

I now wait for 1 to 3 orders to fill and either hit target or stop. If it hits stop I will re-enter a new pending order the same as the one just stopped.

If my targets are hit... GREAT! If not, at Friday 3pm EST I close all open positions and cancel any open pending orders.

Position size for me is 1% of account size on a 50 pip stop loss for each position.

Consistent results with literally only a few MINUTES per week of management.

-----------

## Note

This version doesn't implement the trailing stop.

## Install (local)

Clone the repository

	git clone git@github.com:26medias/bot-lazy-trader.git

Open a console, `cd` into the directory, then install the dependencies:

	npm install

Create env vars to save your Oanda API key & your Oanda Account Number (or hard-code them in app.js line 18 & 19):

	OANDA_KEY=13a5f4284fa8598ac37c4r5e0ef0fa3a-cr45169bceer7b1318c0cde3292o15e9
	OANDA_ACC=101-001-3387465-001

Test locally:

	node test

Unless it's sunday 5pm EST, no positions will be opened.

You can force the code to open positions as if it was time to trade, to ensure that your settings are correct. In app.js on line 15, change `testMode` from `false` to `true`.

## Settings

Open app.js to edit the settings. You can edit:

- Test mode (to force taking positions no matter what day/time it is)
- Risk per position (default at %)
- The pairs to trade (Default: "EUR_USD","EUR_JPY","USD_JPY" but you can add more)
- The timezone to use (defaut is America/New_York)
- The day & time that are valid to open positions (default sunday at 5pm)
- The limit distance (default 30)
- The take-profit distance (default 150)
- The stop-loss distance (default 50)

## Deployment on AWS Lambda

On your AWS console, open **CodeStar**, and filter to only show the templates for Web Services in NodeJS, and select the "AWS Lambda" template.

![CodeStart Step 1](https://i.imgur.com/SOSZRlz.png)

Select a name, then connect your github account. 

![CodeStart Step 1](https://i.imgur.com/v0voCk4.png)

Click **Next** until the end. Leave everything as default.

CodeStar will create a new github repository on your github account. It might take a few minutes.

## Clone the github repository you just created with CodeStar.

Copy the following files into your repository:

- app.js
- bot.js
- core.js
- index.js
- oanda.js
- package.json
- test.js
- .gitignore
- buildspec.yml

## Deploy

To deploy, just push to github. CodeStar will monitor your github repository and trigger a rebuild of your lambda function everytime there is a new commit to `master`.

	git add --all && git commit -m "Base code" && git push

## Configure your lambda function

By default, your lambda function has a 3 seconds timeout & barely any memory allocated. That won't work for this.

Go to your AWS console, then open AWS Lambda.

Look for your bot in the list:

![CodeStart Step 1](https://i.imgur.com/uPDflTK.png)

When you are on your bot's lambda settings, scroll down until you see **Basic Settings**:

![CodeStart Step 1](https://i.imgur.com/BKgjTMv.png)

Even though AWS will charge you more when you use more memory, you also get charged for the execution time. Counter-intuitively, it's usually cheaper to allocate more memory, as it speeds up the execution time quite significantly.

Here are my settings:

![CodeStart Step 1](https://i.imgur.com/YkY3JN2.png)

Now you'll want to setup the env vars, to provide the Oanda API key & account number without having to hard-code them in your code and risk accidentally pushing them to github.

Scroll back up and locate ""

Create the `OANDA_KEY` and `OANDA_ACC` env vars, allong with their values:

![CodeStart Step 1](https://i.imgur.com/jR35jaA.png)

There is a link on your OANDA fxTrade account profile page titled �Manage API Access� (My Account -> My Services -> Manage API Access). From there, you can generate a personal access token to use with the OANDA API, as well as revoke a token you may currently have.

Don't forget to save, and you're ready to test.

## Testing your AWS Lambda deployment

Scroll all the way up, locate the test UI and click "Select a test event":

![CodeStart Step 1](https://i.imgur.com/Y6pjTYL.png)

Create a new test.
Leave the parameters empty (we don't need any), and give it a neme, then click **Create**:

![CodeStart Step 1](https://i.imgur.com/0YaUvMM.png)

Now select & execute your test:

![CodeStart Step 1](https://i.imgur.com/xOo54wn.png)
