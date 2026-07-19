# Cookidoo MCP Server

An MCP (Model Context Protocol) server for interacting with the Thermomix Cookidoo platform, built with `fastmcp`.

> **Disclaimer:** This is an unofficial project. The developers are not affiliated with, endorsed by, or connected to Cookidoo, Vorwerk, Thermomix, or any of their subsidiaries or trademarks.

## Features

- **Authentication**: Connect to your Cookidoo account securely
- **Recipe Details**: Fetch detailed recipe information by ID
- **Recipe Generation**: Structure new custom recipes
- **Recipe Upload**: Upload custom recipes to your Cookidoo account

## Requirements

- **Python 3.12 or newer.** The `cookidoo-api` dependency does not publish wheels for older versions — on Python ≤3.11, `pip install` fails with `No matching distribution found for cookidoo-api`.

## Setup

1. **Clone the repository and navigate to the project directory**

2. **Create a virtual environment and activate it:**
   ```bash
   python3.12 -m venv venv
   source venv/bin/activate  # On macOS/Linux
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure your credentials and locale:**
   ```bash
   cp .env.example .env
   # Edit .env with your Cookidoo credentials
   ```

   > **Important — locale must match your account's country.** Cookidoo data lives on per-country sites (cookidoo.ca, cookidoo.fr, cookidoo.de, …). If `COOKIDOO_COUNTRY`/`COOKIDOO_LANGUAGE` don't match the country your account is registered in, login still succeeds but all your collections and recipes come back **empty**. Defaults are `ca`/`fr-CA`.

5. **Run the MCP server:**
   ```bash
   fastmcp run server.py                                # stdio (for MCP client configs)
   fastmcp run server.py --transport http --port 8000   # HTTP at http://127.0.0.1:8000/mcp
   ```

## Available Tools

Call `connect_to_cookidoo` **first** — the other account tools reuse the session it creates.

- `connect_to_cookidoo` - Authenticate with Cookidoo (required before the tools below)
- `get_recipe_details` - Get detailed recipe by ID (IDs look like `r54208`, visible in recipe URLs)
- `generate_recipe_structure` - Validate free-form ingredients/steps into recipe JSON (no login needed)
- `upload_custom_recipe` - Upload the JSON from `generate_recipe_structure` to your account

## Known Limitations

- **Official recipes have no cooking steps in the API.** `get_recipe_details` returns ingredients, notes, times, utensils and nutrition, but Vorwerk does not expose the step-by-step guided-cooking instructions. Only *custom* recipes (the ones you upload) carry full instructions.
- **No "list my recipes" tool.** The underlying API can enumerate collections but not bookmarked/saved recipes; this server currently only fetches recipes by ID.
- **Login can fail transiently.** A `Login failed: authentication cookies were not set` error right after several logins is usually rate-limiting — wait ~20 seconds and retry. Avoid logging in more than necessary; reuse the session.
- **Cookie-based auth.** Since `cookidoo-api` 0.17 authentication uses session cookies, not Bearer tokens. The Cookidoo endpoints send no CORS headers, so this API cannot be called from a browser page — it must go through a server like this one.

## Acknowledgments

This project is built on top of the [cookidoo-api](https://github.com/miaucl/cookidoo-api), which provides the Python interface to interact with the Cookidoo platform. Special thanks for making this integration possible!

## License

MIT
