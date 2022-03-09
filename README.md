# Nomics Exchange Integration Interview

Welcome to the Nomics Exchange Integration Interview technical challenge.

The goal of this challenge is to give you a real-world example of an integration you would need to implement as part of this role. This helps you and Nomics determine if this work is a good fit for you as a candidate. We aim to make this challenge as close to real world work as possible, so the rules are fairly short and open:

1. You must do this challenge yourself, without anyone else writing code for you.
2. You may use any resource to help you on this challenge, as long as you cite your sources if you use any code. This includes:
  1. Search engines like google
  2. Q&A sites like stack overflow
  3. Technical blogs
  4. Other people (including your interviewer at Nomics)
  5. Really just about any other resource, ask if you're unsure
3. We expect you'll spend around 2 hours on this challenge. Please don't spend more than 3.

Please use your best judgement while spending time on this challenge. We don't want it to be overly burdensome. The goal isn't to finish, it's to provide a solid of example of the work that you do and how you do it.

Additionally, pretend this is like you were working with us, so please feel free to ask your interviewer questions along the way if you get stuck. It's best if you spend most of your time on the challenge, and not just getting stuck on one part. This is a new challenge, so it's possible we may have made mistakes of our own. Please reach out if you find any issues along the way.

## Challenge Context

At Nomics our ingestion system expects APIs to be in our Exchange Integration Specification:

https://github.com/nomics-crypto/nomics-platform/blob/master/doc/cryptocurrency-api-exchange-integration.md

Some exchanges implement this API and submit it to us, while for others we need to write the API adapter ourselves. This "adapter" is what you'll be writing in this challenge, and it sits between and translates the API from the exchange's own format to the Nomics specification:

Exchange API <> Adapter <> Nomics Ingestion System

## Challenge Exchange API

For this challenge we will be using Binance as an example. They have a comprehensive API that is fairly close in structure to our specification.

Here are some relevant links:

* (API Documentation)[https://binance-docs.github.io/apidocs/spot/en/#change-log]
* (API List of Markets)[https://binance-docs.github.io/apidocs/spot/en/#exchange-information]
* (API Candles)[https://binance-docs.github.io/apidocs/spot/en/#kline-candlestick-data]
* (API Order Book)[https://binance-docs.github.io/apidocs/spot/en/#order-book]
* (API Rate Limits)[https://binance-docs.github.io/apidocs/spot/en/#limits]
* (Binance's Markets on Nomics)[https://nomics.com/exchanges/binance/markets]

## Challenge Steps

### Step 1. Set up your Project

Your first step should be to set up your API project. This should be done in JavaScript (TypeScript is optional, just use what you are most comfortable with) on NodeJS using Express.js for the web server.

We recommend following Express.js's Getting Started guide to the point that you have a web server up and running.

### Step 2. Run our Auditor

We provide an auditor that checks an integration for basic correctness and provides some feedback. It's a test suite in a node package so that folks who are implementing our API can quickly test it without having to write their own test suite.

Run our auditor against your project like this:

```
npx nomics-platform@latest audit http://path-to-your-api-root-like-localhost:3000
```

It will immediately give you errors, since you haven't built out the API yet. It's recommended but not required to add the auditor into your project and set it up as your test suite. You can write your own tests too, if you'd like, but that is also not required. Please work however you're comfortable.

### Step 3. Implement /info

The first endpoint you should implement is the (/info)[https://github.com/nomics-crypto/nomics-platform/blob/master/doc/cryptocurrency-api-exchange-integration.md#info---exchange-information---required] endpoint. It returns basic exchange information.

You can fill this out with the bare minimum for the purposes of this exercise.

It's important that you set the following `capability` values:

* markets: true
* ordersSnapshot: true
* candles: true

Those are the three endpoints we will implement as the main part of this challenge.

At this point, when you run the auditor, it should pass on the info endpoint and fail on the markets, ordersSnapshot, and candles endpoints, since they have been enabled in /info but are not yet implemented.

### Step 4. Implement /markets

Now the fun begins! Implement the (/markets)[https://github.com/nomics-crypto/nomics-platform/blob/master/doc/cryptocurrency-api-exchange-integration.md#markets---available-markets---required] endpoint according to our specification, using the Binance API's (Exchange Information)[https://binance-docs.github.io/apidocs/spot/en/#exchange-information] endpoint. You will need to read their documentation and ours to figure out which fields map to which.

### Step 5. Implement /candles

When `/markets` is working we can move on to (/candles)[https://github.com/nomics-crypto/nomics-platform/blob/master/doc/cryptocurrency-api-exchange-integration.md#candles---candles---discouraged] using the Binance API's (Kline/Candlestick Data)[https://binance-docs.github.io/apidocs/spot/en/#kline-candlestick-data]. Note that this endpoint expects a parameter (the market) from the markets endpoint and passes it along to Binance, as well as an interval parameter. It's not very well documented but the intervals are the same as the Nomics intervals.

### Step 6. Implement /orders/snapshot

Finally we can implement the (/orders/snapshot)[https://github.com/nomics-crypto/nomics-platform/blob/master/doc/cryptocurrency-api-exchange-integration.md#orderssnapshot---current-order-book-snapshot---required-for-a-verified-exchanges] API using Binance's (Order Book)[https://binance-docs.github.io/apidocs/spot/en/#order-book] endpoint. Similar to `/candles` it expects a market.

### Step 7. Review

At this point, review your own API and its data and compare against Binance's own market data as well as Nomics's market data for the markets you've implemented. Do your numbers look correct and make sense? Compare to here:

https://nomics.com/exchanges/binance/markets

And you can see an individual market here:

https://nomics.com/exchanges/binance/markets/BTCUSDT

Note that we do process our data so it won't be exactly perfect but it should be fairly close. For some markets our symbols may not exactly match those of Binance.

### Step 8. You're done

If you've reached this point (or a reasonable time limit) it's time to submit your challenge to the interviewer. Just make a zip of your files and email it, and we'll set up a time to meet and review. Remember that we care more about the quality of the work than how far through the challenge you got. Please don't overdo it!
