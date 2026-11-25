# Yelp Reviews Scraper: Can You Get the Hidden Reviews? What Does 1,000 Reviews Cost? Which ScraperAPI Plan Do You Need? (Full Plan Table)

Most people shopping for a Yelp reviews scraper run into one of three surprises: the effective price per review can swing 15x depending on which plan tier you land on, the "not recommended" reviews sit in a separate count that not every tool exposes, and fields you assumed were standard (owner replies, translations, earlier versions of edited reviews) turn out to be optional extras that differ tool by tool.

ScraperAPI sits at the infrastructure end of that market. Plans start at **$49/month for 100,000 credits**, the service handles JavaScript rendering and the anti-bot layer, and what comes back is HTML you parse yourself. Whether that trade suits you depends on how much parsing you want to own, and that is what the rest of this breaks down.

## Quick answer before the details

> Most projects pulling Yelp reviews at real volume should start on **Hobby ($49/mo, 100,000 credits, 20 concurrent threads)** and move up only when the thread ceiling or the credit burn rate becomes the bottleneck. Country-level global geotargeting starts at Business ($299/mo), so if you need yelp.ca or yelp.co.uk content routed through local IPs, Hobby and Startup won't cover it.

👉 [Check ScraperAPI's Hobby plan and current credit pricing](https://www.scraperapi.com/?fp_ref=coupons)

## All ScraperAPI plans, side by side

| Plan | Monthly | Annual (per month, 10% off) | Credits/month | Concurrent threads |  |
| --- | --- | --- | --- | --- | --- |
| Free | $0 | — | 1,000 | 5 | [Start on the free plan](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | $49 | $44.10 | 100,000 | 20 | [Get Hobby](https://www.scraperapi.com/?fp_ref=coupons) |
| Startup | $149 | $134.10 | 1,000,000 | 50 | [Get Startup](https://www.scraperapi.com/?fp_ref=coupons) |
| Business | $299 | $269.10 | 3,000,000 | 100 | [Get Business](https://www.scraperapi.com/?fp_ref=coupons) |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | [Get Scaling](https://www.scraperapi.com/?fp_ref=coupons) |
| Professional | $975 | $877.50 | 10,500,000 | 300 | [Get Professional](https://www.scraperapi.com/?fp_ref=coupons) |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | [Get Advanced](https://www.scraperapi.com/?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22,000,000+ | 500+ | [Talk to ScraperAPI sales](https://www.scraperapi.com/?fp_ref=coupons) |

The columns that actually change your decision sit outside the table:

- **Geotargeting.** Hobby and Startup are limited to US and EU. Country-level global routing starts at Business.
- **Pay-as-you-go.** Available on Scaling, Professional, Advanced and Enterprise. On Hobby, Startup and Business, exhausted credits return a 429 rather than an extra charge.
- **Analytics history.** 30 days on Hobby and Startup. Unlimited from Business up.
- **Support.** Standard through Scaling, priority from Professional, dedicated team plus Slack on Enterprise.

Annual billing takes exactly 10% off, and unused credits reset at each renewal rather than carrying forward. That last detail matters if your Yelp scraping runs in bursts.

## What a Yelp reviews scraper is really being compared on

If you only read the headline "scrapes Yelp reviews," five tools look identical. The differences live in parameter defaults and field lists, which are usually buried mid-documentation rather than on the product page.

### Sorting and star filters: same feature, different granularity

One Apify Actor offers six orders: Yelp default, newest first, oldest first, highest rated, lowest rated, and elites. Its star filter is single-select, meaning all ratings or one specific star level. A competing Actor uses an enumerated sort value with `DATE_DESC` as the default, and its rating filter accepts any combination, so you can ask for four-star and five-star reviews in one run with an empty list meaning everything.

For negative-feedback monitoring, "one-star plus two-star in a single pass" is one run on the second tool and two runs on the first. Small thing, until you're scheduling it weekly across 400 locations.

### Keyword search silently overrides your sort and rating filters

One Actor accepts a free-text query that reviews must match, and it wraps the hit inside the review body with highlight markers. The documentation also states, in passing, that when you set a query, the sort order and the rating filter are ignored, with a single warning in the log. So a run you set up as "newest first, one star only, mentioning parking" actually returns every matching review regardless of order or rating. If your complaint taxonomy depends on that slice, the sample you get is not the sample you asked for.

### Language defaults to English

The same Actor defaults its language code to `en`. Yelp returns only reviews written in that language, so leaving the default in place quietly excludes every non-English review on the listing. Set a non-English code and the translated-text field fills in with Yelp's own translation; leave it at `en` and that field comes back null. Search in German or Chinese with the wrong setting and you can get zero results without any way to tell whether the business genuinely has no such reviews or the parameter never took effect.

### The default cap is 20 reviews per business, not everything

A default value of 20 for maximum reviews per business, where 0 means all available, will catch people out on the first run. Point it at a restaurant with 6,150 reviews and you get 20 records. The natural conclusion is that the tool failed, when the tool did exactly what it was told. A different Actor exposes this as a record limit plus a separate "scrape all review pages" toggle. Migrating scripts between the two means changing this value.

### Hidden reviews are a count in one tool and an endpoint in another

Yelp filters some reviews into "not recommended." One Actor returns that as a number on the business profile record: Gary Danko in San Francisco shows `notRecommendedReviewCount` of **1033** against a displayed `reviewCount` of **6150**. Two separate counters, one record. Another Actor has no not-recommended entry point at all and works only with public reviews. An open-source project claims it retrieves "even the hidden 'not recommended' ones" and lists a dedicated endpoint for them. Whether the Actor that reports the count can also return the text isn't documented, so ask before you buy.

### Owner replies and review edits aren't universal

Owner reply fields exist in at least one Actor as reply text, reply author name and reply timestamp, empty strings when there's no reply. A more comprehensive Actor adds owner reply plus `previousReviewIds`, which tracks earlier versions of a review the same person rewrote. If owner response rate is one of your KPIs, a missing field kills the metric outright rather than degrading it.

For reference, the same Gary Danko listing pulled by two tools a few weeks apart shows star counts of 216 versus 217 for two stars and 4,229 versus 4,228 for five stars, and a language breakdown summing to 6,155 against a review count of 6,150. Those are snapshot differences between crawl times and counting methods, not errors worth reconciling.

## The cost math nobody puts in one table

Apify's Yelp Reviews Scraper page headlines itself at **"$0.5/1000 results."** The pricing table in the body says something different: Actor start costs $0.01, results written to the default dataset cost **$0.0075 per result on the free plan** and **$0.0005 per result on Bronze through Diamond plans**. Multiply it out and $0.0005 × 1,000 = $0.50, matching the headline. But $0.0075 × 1,000 = $7.50, which is 15 times the headline. The advertised rate applies to paid plan tiers only; on the free plan the same thousand results cost 15x more. The page also requires a maximum-charge limit of at least $0.10 per run, which is a spending cap, not a minimum fee. Budget your first test run off the headline and the invoice will disagree with you.

ScrapeHero bills its Yelp Reviews Scraper at **2 data credits per record** and does not publish a dollar price for a data credit anywhere on that page. You cannot convert its per-record cost without asking support. Opaque units like this are exactly what gets skipped in comparison tables.

The open-source omkarcloud Yelp scraper advertises itself as free, open source and unlimited. Its README is more specific: the search, search filters, menus and related-businesses endpoints sit behind DataDome and require a `YELP_PROXY` (a sticky US residential proxy) plus a `CAPSOLVER_API_KEY` to unlock. The remaining endpoints work out of the box. "Free" here means free software plus self-funded residential proxies and CAPTCHA-solving service. The project describes itself as covering 8M+ businesses, 330M+ reviews, 28 endpoints and 1,500+ Yelp categories. Those are vendor-stated figures with no third-party verification behind them.

Outscraper's Python SDK requires an API token from its own dashboard and the page lists no pricing.

The practical conclusion: you cannot rank these tools by "cost per review." They bill in results, in data credits, in requests, or in software-plus-proxy. Converting them into a single number produces a table that looks authoritative and is wrong.

## What ScraperAPI actually gives you on Yelp

ScraperAPI has no Yelp-specific parser. Its own Yelp scraping guide walks through sending a request, getting HTML back, and pulling fields with BeautifulSoup by selecting on the star-rating aria-label, the review paragraph by language attribute and the date span. Reviews load via JavaScript and paginate through a `start` parameter (`&start=10`, `&start=20` and so on), so the Render Instruction Set is what makes the page arrive fully executed before you parse it.

That rendering and anti-bot work is what you're paying for, and the credit rules are published. A standard page costs 1 credit. Amazon costs 5. Google and Bing cost 25. LinkedIn costs 30. Pages behind Cloudflare, DataDome or PerimeterX add **10 credits per request** when ScraperAPI activates bypass logic. The dashboard's Domain Cost Estimator is the authoritative per-URL figure, and you can set a `max_cost` to cap any single request.

Yelp is a site that actively blocks scrapers, so budget for the surcharge rather than the base rate:

| Scenario | Credits per page request | Page requests on Hobby (100,000 credits) | Reviews at 10 per page | Effective cost per review |
| --- | --- | --- | --- | --- |
| No bypass triggered | 1 | 100,000 | ~1,000,000 | ~$0.00005 |
| Bypass triggered every time | 11 | ~9,090 | ~90,900 | ~$0.00054 |

Even at the pessimistic end, $49 buys you roughly the same per-review cost as Apify's paid-tier rate of $0.0005 per result. What you pay in exchange is engineering time: you write the parser, you maintain the field mappings, and you handle the cases where a selector silently returns nothing.

Concurrency scales from 20 threads on Hobby to 500 on Advanced. Going over your plan's limit returns a 429 with no extra charge, so the request is turned away rather than queued and billed. By default scraping pauses until credits renew. Auto-upgrade, which moves you to the next tier when credits run dry, is available from Hobby through Professional; pay-as-you-go at a fixed per-credit rate is available from Scaling upward.

👉 [See how ScraperAPI's credit pricing maps to your Yelp volume](https://www.scraperapi.com/?fp_ref=coupons)

## Who should not buy this

If you need one or two businesses and a few hundred reviews for a one-off analysis, a $49 monthly minimum against that volume is bad math. Apify's free plan tier or the open-source project with your own proxy is cheaper for that job.

If what you want is "paste a business URL, get structured JSON with fields already named," ScraperAPI is the wrong shape. You'd be writing the parser. The prebuilt Actors are the right shape, and their documentation is honest about it.

If you need business details, menus and reviews in one pipeline, a reviews-only scraper won't get you there. One Actor states outright that it does reviews only and won't search businesses or pull profiles and menus. The more comprehensive Actor does return a business profile record, but claimed status, year established, summary, specialties, attributes and health inspections come back null in recent runs. A review scraper is not a business-data scraper with extra fields.

One more constraint worth naming: review data includes personal data such as reviewer display names, public locations, profile photos and review text. That falls under GDPR and comparable privacy rules in other jurisdictions, so your handling obligations travel with the dataset regardless of which vendor produces it.

## Final decision rules

- **A few hundred reviews, one-time analysis:** use a free plan tier or the open-source project. Don't pay a monthly minimum.
- **Tens of thousands to hundreds of thousands of reviews, custom fields, your own parsing logic:** ScraperAPI from Hobby up. The credit model gives you cost control that per-result billing doesn't.
- **Yelp Canada or Yelp UK local content:** Business tier and above. Hobby and Startup route through US and EU proxies only.
- **You need the text of not-recommended reviews:** confirm with the vendor before paying. One tool reports the count, one skips the category entirely, and one claims a dedicated endpoint. Don't assume any default behavior.

Two things to confirm before a large run: the actual credit cost for your specific Yelp URLs, which you can check in the dashboard's Domain Cost Estimator rather than estimating from the table above, and whether 20 concurrent threads clears your throughput requirement or leaves you queuing.

👉 [Start with ScraperAPI's free 1,000 credits and size the plan from there](https://www.scraperapi.com/?fp_ref=coupons)
