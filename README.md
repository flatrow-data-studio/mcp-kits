# MCP kits for Apify: hotel price agent and e-commerce price agent

> The Actors of these kits are paid Apify Actors built by Flatrow Data Studio (the author of this repository).

Ready-made configurations of the [Apify MCP server](https://mcp.apify.com) for AI assistants. Each kit pins six Actors that work together, so the agent calls them directly instead of searching Apify Store, and gives it a system prompt that says which tool answers which question.

| Kit | Tools | Ask it |
|---|---|---|
| [Hotel price agent](hotel-price-agent.md) | Booking.com, Google Hotels, Agoda, Expedia, Airbnb search, Airbnb listing pages | "Compare hotel prices in Lisbon next weekend on Booking.com, Agoda and Google Hotels" |
| [E-commerce price agent](ecommerce-price-agent.md) | Price comparison across Amazon, AliExpress, eBay, Walmart and Etsy in one run, plus one search tool per marketplace | "What is the cheapest marketplace for wireless earbuds under $50?" |

Each kit contains:

- the MCP server URL;
- how to connect it to Claude, Claude Code, ChatGPT, Cursor and VS Code;
- a system prompt of about 900 words: tools, real example inputs, output columns, honest limits and cost;
- four test prompts to check the setup.

## All 16 Actors in one server

The official [MCP Registry](https://registry.modelcontextprotocol.io) lists the whole set as `io.github.flatrow-data-studio/hotel-and-product-prices` ([server.json](server.json)): one remote server with the 16 Actors as tools, for registry-aware clients and directories. The kits above pin six Actors each and add a system prompt, which works better for a dedicated agent.

You need an Apify account (the Free plan includes monthly credit). The Actors are paid per result and collect no personal data.

## More

- All Actors of the studio: [apify.com/myth84](https://apify.com/myth84)
- Python examples: [github.com/flatrow-data-studio](https://github.com/flatrow-data-studio)
