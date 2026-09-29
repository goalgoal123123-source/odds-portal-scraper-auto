# Automation notes (fork changes)

This repo is a fork of [gingeleski/odds-portal-scraper](https://github.com/gingeleski/odds-portal-scraper)
(Unlicense — public domain) with minimal changes so it can run **fully automatically**
on GitHub Actions (free, no extra hosting, no user interaction):

1. **Selenium 3 → 4** (`full_scraper/oddsportal/crawler.py`, `full_scraper/oddsportal/scraper.py`,
   `full_scraper/requirements.txt`): the pinned `selenium==3.141.0` cannot drive modern Chrome.
   Updated `find_element_by_css_selector` → `find_element(By.CSS_SELECTOR, …)`,
   `webdriver.Chrome('./chromedriver/chromedriver', chrome_options=…)` →
   `webdriver.Chrome(options=…)` (Selenium Manager auto-downloads the matching driver),
   and always-on headless flags (`--headless --no-sandbox --disable-dev-shm-usage`).
2. **Stale 2019 sample outputs removed** from `full_scraper/output/`; the workflow repopulates it.
3. **`.github/workflows/scrape.yml`**: `workflow_dispatch` + daily cron (`0 2 * * *` UTC = 10:00 HKT).
   It installs deps, pipes `1` into `python op.py` (answers the interactive sport-selection
   prompt; the bundled config lists a single league), then commits any new files under
   `full_scraper/output/` back to the repo.

Current run config: `full_scraper/config/ohl-home-away.json` (OHL hockey 2019/2020, DEBUG smoke
mode = first season page only). To scrape a different league/season, point `TARGET_SPORTS_FILE`
in `full_scraper/op.py` at another config in `full_scraper/config/` (e.g. `sports.json`) and
adjust `DEBUG`.

No API keys or logins are needed — the scraper only reads public results pages.
