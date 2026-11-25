# Google SERP scraper costs: how many credits one search burns, what 1,000 searches cost, and which plan covers a daily keyword list (credit math + endpoint checklist)

ScraperAPI bills every Google and Bing request at **25 credits**, not one, so the plan numbers read very differently once you convert them. The 1,000 free credits are 40 Google searches, not 1,000. Startup costs **$149/month** for 1,000,000 credits, which is 40,000 searches, or about **$3.73 per 1,000 Google results pages**. The rest of this page does the conversion plan by plan, lists the add-ons that raise the per-request cost, and covers the endpoint parameters and limits worth checking before you pay.

> **Most readers:** a keyword list refreshed daily fits comfortably on Startup, since 40,000 searches a month is roughly 1,300 keywords a day. Testing the water first costs nothing beyond the 40 free searches. 👉 [see the Startup plan and its current credit allowance](https://www.scraperapi.com/pricing?fp_ref=coupons)

## All ScraperAPI plans, converted into Google searches

One column here does most of the work. Credits are the billing unit; Google searches are what you actually came for, and the conversion is credits ÷ 25.

| Plan | API credits / month | Google SERP requests | Concurrent threads | Price | Get it |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 40 | 5 | $0 | [ Free plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 | 4,000 | 20 | — | [ Hobby](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 40,000 | 50 | **$149/mo**, or $134.10/mo billed annually ($1,609.20/yr) | [ Startup](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 120,000 | 100 | — | [ Business](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 | 200,000 | 200 | — | [ Scaling](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 420,000 | 300 | — | [ Professional](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 860,000 | 500 | — | [ Advanced](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 880,000+ | 500+ | — | [ Enterprise](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Where the price shows a dash, the monthly figure moves with the billing cycle and both rates sit on the plan card. Annual billing is worth pricing out: on Startup it takes the effective monthly rate from $149 to $134.10 and saves $178.80 over the year.

Two rows in that table matter more than the others for cost. Geotargeting is US and EU only on Free, Hobby and Startup; country-level targeting starts at Business. Analytics history is 30 days on those same two paid tiers and unlimited from Business up. If your ranking reports are country-specific outside the US and EU, the jump to Business is a feature requirement, not a volume upgrade.

## The add-ons that change the 25-credit figure

A Google request is 25 credits on its own. Several parameters stack on top of that, and this is where budgets quietly break.

| Request type | Credits | Note |
| --- | --- | --- |
| Standard page | 1 | Per request |
| Google or Bing SERP | 25 | Per request, subdomains included |
| JavaScript rendering (`render=true`) | +10 | Per request |
| Premium proxies (`premium=true`) | +10 | Per request |
| Ultra premium (`ultra_premium=true`) | +30 | Paid plans only |
| Ultra premium + JS rendering | 75 | Paid plans only |
| Bot-protection bypass | +10 | Cloudflare, DataDome, PerimeterX and similar |

Geotargeting, sessions, device type, output format and auto parsing cost nothing extra. If you send Google requests with JS rendering and premium proxies together, you are at 45 credits per page, and the 1,000,000 credits on Startup buy 22,222 searches instead of 40,000. Every response returns its own cost in the `sa-credit-cost` header, so you can log actual spend rather than forecast it, and `max_cost` caps any single scrape so one badly configured URL can't drain a day's budget.

One habit worth building before a big run: check the cost of your exact URL and parameter set in the dashboard's Domain Cost Estimator, then multiply by your monthly keyword count. 👉 [check what a Google SERP request costs on the plan you're considering](https://www.scraperapi.com/pricing?fp_ref=coupons)

## What 1,000 searches actually costs

Only one tier has a price you can divide cleanly. Startup runs $149 for 1,000,000 credits, which is $0.000149 per credit. At 25 credits per Google request that is $0.003725 each, or **$3.73 per 1,000 searches**. On annual billing the same maths gives **$3.35 per 1,000**. A 5,000-keyword monthly crawl lands around $18.60.

For a public comparison point on billing models, one Apify SERP actor prices itself at $0.002 per dataset item plus a small start fee, which sounds cheaper per row until you count rows: one search page can produce 10 organic results, a summary row, and error rows for blocked requests. Per-item pricing scales with how much the page returns; per-request pricing scales with how many pages you ask for. ScraperAPI is the second kind, which makes the forecast simpler and the cost per keyword fixed.

## The free plan buys 40 searches, so spend them carefully

The free tier gives you 1,000 credits and 5 concurrent connections. Divided by 25, that is 40 Google SERP requests, which is enough to run a real field check on your hardest queries and not much else. Treat it as a technical validation budget rather than a trial month.

Pick the ten queries you care most about, run each one twice, and look at the payload. Do you get position, title, URL, displayed URL and description for every organic result? Do the ads and People Also Ask blocks come back where they exist? Does the `start` offset return a genuinely different page of results rather than a reshuffle? Forty requests answer all of that. 👉 [run your first 40 searches on the free credits](https://www.scraperapi.com/pricing?fp_ref=coupons)

## What the Google SERP endpoint accepts

ScraperAPI's structured Google endpoint is asynchronous. You POST JSON to `async.scraperapi.com/structured/google/search` with your `apiKey` and either a single `query` or a `queries` array for batch work, plus a `callback` object if you want a webhook fired when the job finishes. Output is JSON by default or CSV on request.

Useful parameters for rank tracking:

- `gl` and `hl` set the results country and interface language.
- `country_code` and `tld` work together. Scraping google.com from Canada needs both, or Google serves you the wrong regional index.
- `start` handles pagination by result offset.
- `tbs` filters by time, with `tbs=d` for the past day and `tbs=h` for the past hour.

Pagination is worth budgeting for explicitly. Page two is a second request and a second 25 credits, so a 10-page pull on 1,000 keywords is 10,000 requests, or 250,000 credits. Most rank tracking needs page one only.

## Check these response fields before you commit

A scraper that returns rank and URL but nothing else will leave your report half-empty. Run one request from the free credits and check for these:

1. Position, title, destination URL, displayed URL and snippet for each organic result. Without `displayedUrl` you cannot spot sitelinks or breadcrumb formatting that affects click-through.
2. Ads, split into top and bottom blocks, if paid coverage is part of your reporting.
3. People Also Ask questions and related searches, since these drive content briefs as much as rankings do.
4. A timestamp per record. Rankings without a collection time are unusable in a time series.
5. Consistent row counts. Some SERP tools emit one extra merged row per keyword covering all pages, which silently doubles your keyword totals in any pivot table.
6. A note on where derived fields come from. Related-query lists that are rebuilt from organic titles look identical to native Google suggestions in a JSON file, and AI overview text sometimes requires a separate external key. Ask which is which before those fields reach a client report.

## What happens when Google pushes back, or you hit a limit

Concurrency limits are enforced, not queued. Exceed your plan's thread count and the request gets a 429 telling you to bring concurrency back in line. Nothing extra is charged for that rejection, but nothing gets scraped either, so set your worker count to the plan limit rather than the machine's capacity.

When credits run out, scraping pauses until the next renewal by default. Hobby, Startup, Business, Scaling and Professional can enable auto-upgrade, which moves you up a tier when the balance hits zero. Scaling, Professional, Advanced and Enterprise additionally support pay-as-you-go at a fixed per-credit rate up to a monthly cap you set. Annual plans can renew early for a fresh credit batch, which helps if your usage is seasonal.

Blocked requests are the other failure mode, and the credit system gives you a lever: escalating to premium or ultra-premium proxies raises the per-request cost, so you can reserve expensive routing for the queries that need it. There is no published success rate for Google SERP requests, so measure it yourself on the free credits against your own keyword set. 👉 [compare the plans that include pay-as-you-go overage](https://www.scraperapi.com/pricing?fp_ref=coupons)

On legality, the position most providers settle on is the conservative one: scraping publicly available search results may be restricted by Google's Terms of Service, and compliance is the account holder's responsibility. Search results can also contain personal data, which falls under GDPR and similar regimes in the EU. Set polite request rates, keep only what your reporting needs, and get legal input if you are collecting at scale or reselling the data.

## FAQ

**Does one Google search really cost 25 credits?**
Yes, Google and Bing requests are priced at 25 credits per request, subdomains included, against 1 credit for a standard page. Add JS rendering or premium proxies and it climbs.

**Can I get more than the first page of results?**
Yes, using the `start` offset. Each page is a separate request at 25 credits.

**Can I scrape Google Shopping, Images or News?**
The structured endpoint documented here covers Google Search results, with parameters for country, language, time filter and pagination. Shopping, Images and News are separate surfaces; confirm support with the provider before you build a pipeline around them.

**What happens if my keyword list outgrows my plan mid-month?**
Scraping pauses at zero credits unless auto-upgrade or pay-as-you-go is switched on. Both are opt-in and both are visible in billing before you enable them.

**Is annual billing cheaper?**
On Startup, $134.10/month billed annually against $149 monthly, a $178.80 saving over the year.

## Which plan to buy

Under 100 keywords, refreshed weekly: Hobby at 100,000 credits covers 4,000 searches, around 130 keywords a day. Between 100 and 1,300 keywords daily: Startup, where the fixed price and 40,000 searches line up with a normal rank-tracking cadence. Above that, or needing country-level geotargeting: Business and up, where both the volume and the targeting change together. Running a one-off research pull on a few dozen terms: stay on the free credits and buy nothing.

Two things to confirm at checkout. First, whether your requests will bill at the 25-credit SERP rate or higher because you enabled JS rendering or premium proxies, since that halves or thirds the search count. Second, how overage behaves on the tier you pick, because pausing mid-month is a different problem from a capped pay-as-you-go bill.

👉 [open the ScraperAPI pricing page and match credits to your keyword count](https://www.scraperapi.com/pricing?fp_ref=coupons)
