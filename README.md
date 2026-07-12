# Liège Automated Housing Finder & Scraper 🇧🇪🏠

An automated tool designed to scrape, filter, and track apartment and student housing listings in Liège, Belgium — and contact landlords on your behalf.

Finding accommodation or a "kot" (student room) in Liège is incredibly competitive. Listings disappear within hours. This open-source project automates the exhausting process of constantly refreshing real estate websites, filtering results by your exact criteria, and reaching out to landlords the instant a matching property becomes available.

## ✨ Key Features

- **Automated Scraping:** Regularly scrapes top Belgian student housing platforms — [KotaLiège](https://www.kotaliege.be) and the [ULiège housing database](https://logement.uliege.be) — for every listing in the Liège region.
- **Automated Outreach:** Sends your message to matching landlords via the platform's contact form, email (Gmail SMTP), or WhatsApp — so you never have to copy-paste again.
- **Custom Filters:** Filter by total price range (rent + charges), lease duration, domiciliation policy, availability date, and housing type (kots, studios, colocations).
- **Three Price Tiers:** Results are categorized into ≤ 400 €, 401–450 €, and 451–500 € for quick decision-making.
- **Duplicate Prevention:** A persistent JSON log tracks every listing already contacted — re-runs only message new listings, never the same landlord twice.
- **Multi-Platform:** Two independent bots, each tailored to its platform's structure and contact method.

## 🛠️ Tech Stack & Requirements

- **Language:** Python 3.10+
- **Browser Automation:** [Playwright](https://playwright.dev/python/) (headless Chromium)
- **HTTP Scraping:** Requests + BeautifulSoup (for the ULiège bot)
- **Email:** Gmail SMTP (App Password)
- **WhatsApp:** Playwright driving WhatsApp Web
- **No database required** — all state is stored in local JSON files.

## 📁 Project Structure

```
liege_housing_automated/
├── kotaliege_automated/    # Bot for kotaliege.be
│   ├── run.py              # Main entry point — scrape, filter, message
│   ├── scraper.py          # Listing scraper
│   ├── contacted.json      # Persistent log of contacted listings
│   └── README.md           # Setup & usage
├── logement_uliege/        # Bot for logement.uliege.be
│   ├── run.py              # Scrape & collect contacts
│   ├── outreach.py         # Email sender (Gmail SMTP)
│   ├── whatsapp_send.py    # WhatsApp sender
│   ├── processed.json      # Persistent log of contacted landlords
│   └── README.md           # Setup & usage
└── README.md               # This file
```

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or higher
- A KotaLiège account (free, register at [kotaliege.be](https://www.kotaliege.be/_register_))
- (Optional) A Gmail App Password for automated emails
- (Optional) WhatsApp Web access for automated messages

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/ahmad-36/liege_housing_automated.git
   cd liege_housing_automated
   ```

2. Install dependencies:
   ```bash
   pip install -r kotaliege_automated/requirements.txt
   pip install -r logement_uliege/requirements.txt
   playwright install chromium
   ```

3. Configure your credentials:
   - **KotaLiège:** Fill in `kotaliege_automated/credentials.json` with your email and password.
   - **ULiège emails:** Fill in `logement_uliege/email_credentials.json` with your Gmail address and App Password.

4. Run the scraper:
   ```bash
   # KotaLiège — scrape + message new listings
   python kotaliege_automated/run.py

   # Preview only (no messages sent)
   python kotaliege_automated/run.py --dry-run

   # ULiège — scrape + collect contacts
   python logement_uliege/run.py

   # ULiège — send emails to new contacts
   python logement_uliege/outreach.py
   ```

## 🗺️ Targeted Locations

The scraper covers all major residential and student hubs in Liège:

| Area | Zip Code | Notes |
|------|----------|-------|
| Liège Centre / Cathédrale | 4000 | City center, close to HEC & downtown campus |
| Outremeuse | 4020 | Popular student quarter across the Meuse |
| Angleur / Sart-Tilman | 4031 | Closest to ULiège main campus |
| Guillemins / Avroy | 4000 | Near the TGV station |
| Botanique / Saint-Gilles | 4000 | Dense student housing area |
| Fragnée / Val Benoît | 4000 | Near the converted Val Benoît campus |

## 🔍 How It Works

1. **Scrape** — Playwright opens a headless browser, navigates through every page of every listing type (kots, studios, kots-chez-l'habitant, colocations), and extracts rent, charges, size, neighborhood, duration, domiciliation policy, and availability.
2. **Filter** — Listings are filtered against your criteria: total budget ≤ 500 €, domiciliation not refused, available from your target date.
3. **Deduplicate** — The persistent `contacted.json` log is checked. Any listing already messaged is skipped.
4. **Contact** — For new matches, the bot navigates to each listing's detail page and submits your message through the platform's contact form. On ULiège, it sends personalized emails and WhatsApp messages instead.
5. **Log** — Every action is recorded with timestamp, status, and listing details. Re-runs pick up exactly where you left off.

## ⚙️ Configuration

Edit the filter criteria at the top of each `run.py`:

```python
# Budget tiers (total = rent + charges)
TIER_1 = 400   # ≤ 400 €
TIER_2 = 450   # 401–450 €
TIER_3 = 500   # 451–500 €

# Availability window
AVAILABLE_FROM = "2026-08-01"  # August or later

# Housing types to scrape
LISTING_TYPES = ["kots", "studios", "kots-chez-l-habitant", "colocations"]
```

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! If you want to add support for a new Belgian housing website (Immoweb, Brukot, Kotanamur…), feel free to open a Pull Request.

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

## ⚠️ Disclaimer

Built for a personal housing search during an exchange semester. If you reuse it:

- Keep request volumes low and add delays between requests.
- Respect the platforms and the people behind the listings.
- Automating WhatsApp violates its Terms of Service and can get your number banned.
- Always check a platform's Terms of Service before running automated tools against it.
