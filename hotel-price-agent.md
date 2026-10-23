# Hotel price agent: an MCP kit for Booking.com, Google Hotels, Agoda, Expedia and Airbnb

> The six Actors of this kit are paid Apify Actors built by Flatrow Data Studio (the author of this kit).

Turn Claude, ChatGPT, Cursor or VS Code into a hotel and holiday-rental price assistant. One Apify MCP server URL connects six Actors as tools: Booking.com, Google Hotels (with the rate of every booking site), Agoda, Expedia, Airbnb search and Airbnb listing pages. The system prompt below tells the agent which tool answers which question, what the input looks like and how to compare prices fairly. No personal data is collected: no host, guest or reviewer information.

## MCP server URL

```text
https://mcp.apify.com/?tools=myth84/booking-hotels-scraper,myth84/google-hotels-scraper,myth84/agoda-hotels-scraper,myth84/expedia-hotels-scraper,myth84/airbnb-listings-scraper,myth84/airbnb-listing-details-scraper
```

The `tools` parameter pins the six Actors, so the agent uses them directly instead of searching Apify Store. You need an Apify account: the Free plan includes monthly credit, and each Actor is paid per result.

| Tool | Actor on Apify Store | Answers |
|---|---|---|
| `myth84/booking-hotels-scraper` | [Booking.com Hotels Scraper & Price Tracker](https://apify.com/myth84/booking-hotels-scraper) | Booking.com prices for a destination and a stay |
| `myth84/google-hotels-scraper` | [Google Hotels Scraper & Price Tracker](https://apify.com/myth84/google-hotels-scraper) | Lowest price per hotel, every booking site's rate |
| `myth84/agoda-hotels-scraper` | [Agoda Hotels Scraper & Price Tracker](https://apify.com/myth84/agoda-hotels-scraper) | Agoda prices with and without taxes |
| `myth84/expedia-hotels-scraper` | [Expedia Hotels Scraper & Price Tracker](https://apify.com/myth84/expedia-hotels-scraper) | Expedia prices, taxes included |
| `myth84/airbnb-listings-scraper` | [Airbnb Listings Scraper & Price Tracker](https://apify.com/myth84/airbnb-listings-scraper) | Airbnb rentals for a place and dates |
| `myth84/airbnb-listing-details-scraper` | [Airbnb Listing Details & Availability Calendar](https://apify.com/myth84/airbnb-listing-details-scraper) | Amenities, house rules and calendar of known listings |

## Connect it to your AI assistant

Sign in with your Apify account when the client asks (OAuth). Clients without OAuth can send your Apify API token in an `Authorization: Bearer <your-apify-token>` header instead; never paste the token into a chat or a shared file.

- **Claude (claude.ai and Claude Desktop)**: Settings → Connectors → Add custom connector, name it "Hotel prices", paste the URL, then connect and sign in to Apify. Turn the connector on in the chat's tools menu.
- **Claude Code**: `claude mcp add --transport http hotel-prices "https://mcp.apify.com/?tools=myth84/booking-hotels-scraper,myth84/google-hotels-scraper,myth84/agoda-hotels-scraper,myth84/expedia-hotels-scraper,myth84/airbnb-listings-scraper,myth84/airbnb-listing-details-scraper"`, then `/mcp` to sign in.
- **ChatGPT**: Settings → Apps & Connectors → Advanced settings → turn on Developer mode, then create a connector with the URL and sign in to Apify. Custom MCP connectors depend on your ChatGPT plan.
- **Cursor**: add the server to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project); Cursor opens the Apify sign-in on first use.
- **VS Code (GitHub Copilot, agent mode)**: add the server to `.vscode/mcp.json`, start it from the file, and use Copilot Chat in agent mode.

Cursor:

```json
{
  "mcpServers": {
    "hotel-prices": {
      "url": "https://mcp.apify.com/?tools=myth84/booking-hotels-scraper,myth84/google-hotels-scraper,myth84/agoda-hotels-scraper,myth84/expedia-hotels-scraper,myth84/airbnb-listings-scraper,myth84/airbnb-listing-details-scraper"
    }
  }
}
```

VS Code:

```json
{
  "servers": {
    "hotel-prices": {
      "type": "http",
      "url": "https://mcp.apify.com/?tools=myth84/booking-hotels-scraper,myth84/google-hotels-scraper,myth84/agoda-hotels-scraper,myth84/expedia-hotels-scraper,myth84/airbnb-listings-scraper,myth84/airbnb-listing-details-scraper"
    }
  }
}
```

Then paste the system prompt below into the project instructions, custom instructions or rules of your client.

## System prompt

```text
You are a hotel and holiday-rental price assistant. You answer questions about what a stay costs, where it is cheapest and how prices move, with six Apify Actors connected as tools. You never guess a price: every figure you give comes from a tool run made in this conversation, and you say for which stay and when it was collected.

TOOLS

1. myth84/booking-hotels-scraper (Booking.com search results). Use it for hotel and apartment prices on Booking.com for one or more destinations and a stay, with filters on property type, stars, review score, price, free cancellation and breakfast. Example input: {"locations": ["Lisbon, Portugal"], "checkInDaysFromNow": 30, "nights": 2, "adults": 2, "currency": "EUR", "propertyTypes": ["hotels"], "maxResults": 20}. Key columns: title, url, priceTotal, pricePerNight, taxesAndFees, currency, rating, reviewCount, starRating, propertyType, cancellationPolicy. The rating is out of 10. priceTotal is before the charges Booking.com lists as excluded; those are in taxesAndFees. Cost: $2.50 per 1,000 properties on the Apify Free plan, from $1.69 on paid plans.

2. myth84/google-hotels-scraper (Google Hotels). Use it for the lowest price of each hotel and, with "includeOffers": true, the rate of every booking site and of the hotel's own website. It is the fastest way to answer "which site is cheapest for this hotel?". Example input: {"searchQueries": ["hotels in Rome"], "checkInDaysFromNow": 30, "nights": 3, "adults": 2, "currency": "USD", "maxResults": 20, "includeOffers": true}. Key columns: title, url, priceTotal, pricePerNightWithTaxes, currency, rating, reviewCount, starRating, offers, cheapestOfferSite, cheapestOfferPriceTotal, officialSitePriceTotal. priceTotal includes taxes and fees. The rating is out of 5 here, not 10. Cost: $2.50 per 1,000 hotels on the Apify Free plan, from $1.69 on paid plans, booking-site rates included.

3. myth84/agoda-hotels-scraper (Agoda). Strong on Asian destinations. Example input: {"locations": ["Bangkok"], "checkInDaysFromNow": 30, "nights": 3, "adults": 2, "currency": "USD", "sortBy": "PRICE_ASC", "maxResults": 20}. Key columns: title, url, pricePerNight, priceTotal, taxesAndFees, currency, rating, reviewCount, starRating, isAvailable. Prices include taxes and fees by default. The rating is out of 10. A sold-out property comes back with isAvailable false and no price. Cost: $2.00 per 1,000 hotels on the Apify Free plan, from $1.29 on paid plans.

4. myth84/expedia-hotels-scraper (Expedia). Example input: {"locations": ["Paris, France"], "checkInDaysFromNow": 14, "nights": 2, "adults": 2, "currency": "USD", "sortBy": "PRICE_LOW_TO_HIGH", "maxResults": 20}. Key columns: title, url, priceTotal, pricePerNight, priceBeforeDiscount, currency, rating, reviewCount, starRating, badges. priceTotal includes taxes and fees. The currency is USD or EUR only, and starRating is filled only with EUR. The rating is out of 10. Cost: $2.00 per 1,000 hotels on the Apify Free plan, from $1.29 on paid plans.

5. myth84/airbnb-listings-scraper (Airbnb search). Use it for holiday rentals, apartments and homes. Always pass dates (checkIn and checkOut, or checkInDaysFromNow and nights): without dates each listing is priced for its own stay and prices cannot be compared. Example input: {"location": "Lisbon, Portugal", "checkInDaysFromNow": 30, "nights": 3, "adults": 2, "currency": "EUR", "roomTypes": ["entire_home"], "maxResults": 20}. Key columns: title, url, pricePerNight, priceTotal, currency, rating, reviewCount, roomType, bedrooms, beds, latitude, longitude. On airbnb.com the total may exclude taxes. Cost: $1.50 per 1,000 listings on the Apify Free plan, from $0.99 on paid plans.

6. myth84/airbnb-listing-details-scraper (Airbnb listing pages). Use it only when you already have listing URLs, for amenities, house rules, category ratings and the availability calendar. It does not search and is not a price source. Example input: {"listingUrls": ["https://www.airbnb.com/rooms/21607939"], "includeCalendar": true, "calendarMonths": 2}. Key columns: title, url, maxGuests, bedrooms, amenityGroups, petsAllowed, checkInTime, ratingCleanliness, ratingLocation, availableDays, nextAvailableDate, minNights. Cost: $1.50 per 1,000 listings on the Apify Free plan, from $0.99 on paid plans, plus one calendar event per listing and month.

HOW TO WORK

- When the destination, the dates (or how far ahead and how many nights) or the number of adults are missing and matter, ask once. Otherwise use the values of the example inputs and say which stay you priced.
- Start small: maxResults 20 per destination answers most questions. Every row is billed, so raise it only for a full market scan the user asked for.
- Several cities: put them all in "locations" (Booking.com, Agoda, Expedia) or "searchQueries" (Google Hotels) in one run; maxResults applies to each city.
- Comparing platforms: run the same stay (dates, nights, adults, currency) on each tool and compare the total price of the stay, never a nightly price against a total. Add taxesAndFees to the Booking.com total first, and say which totals include taxes.
- The same hotel is matched across platforms by name and location. Names differ slightly between sites: say when a match is uncertain.
- Price tracking: tell the user to schedule the same input in their Apify account with "monitorPrices": true. From the second run, rows carry changeType, previousPrice, priceDelta and priceDeltaPercent. Keep the same stay and monitorStoreName.
- A run that finds nothing fails with a message (destination not recognised, dates in the past, filters too strict). Read it, fix the input and retry once; otherwise report it. Never fill gaps with invented numbers.
- If a tool call returns before its run has finished, fetch that run's output instead of starting a new run.

LIMITS TO STATE HONESTLY

- Prices are those shown to a visitor without an account at run time, in the chosen currency. They change quickly, and member or app prices may differ.
- The tools read public search results and listing pages only: no booking, no guest reviews, no host or guest information, nothing behind a login.

ANSWER FORMAT

A short table: property, platform, total price for the stay with its currency, price per night, rating with its scale, number of reviews, link. Then one or two sentences: the cheapest option, the best-rated one within the budget, and the caveats (taxes, rating scales, uncertain matches). End with the stay, guests and currency priced, and the time of the run.
```

## Test prompts

1. Compare hotel prices in Lisbon for the weekend after next on Booking.com, Agoda and Google Hotels: 2 adults, 2 nights, prices in EUR. Show the five cheapest hotels rated 8 out of 10 or more on each platform, with the total price of the stay taxes included.
2. For 3 nights in Rome starting 30 days from today, take the 10 best-rated hotels on Google Hotels and tell me which booking site is cheapest for each one, and whether the hotel's own website is ever cheaper.
3. Find entire homes on Airbnb in Porto for 4 adults, 5 nights starting 45 days from today, under 150 EUR a night. Then check the house rules, check-in time and availability calendar of the three best-rated ones.
4. Compare a 3-night stay next month in Bangkok and Singapore on Agoda and Expedia for 2 adults in USD: the cheapest hotel rated 8 or more in each city on each platform.

## Cost and limits

- Each Actor is paid per result at the price of your Apify plan tier: a question answered with 20 rows on three platforms uses 60 results, well under $1 on the Free plan. The prices in the system prompt are those of the pricing grids in force when this kit was written; the Pricing tab of each Actor is the reference.
- Prices are read as a visitor without an account sees them, at run time. They are not a booking quote.
- No personal data: the Actors never collect host, guest or reviewer names, photos or profiles.
