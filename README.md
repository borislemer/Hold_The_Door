# Malicious IP Crawler

This project was created as **another consortium of malicious IPs available online**. It aggregates public blocklists and threat feeds into a single place, deduplicated and dated, so you can **easily update blocking rules** in firewalls, WAFs, or other security controls without chasing multiple sources or formats.

**This project solely exists to make it easy to consume and maintain blocking rules** — no analytics, no sign-up, just pull the lists and point your systems at them (e.g. via raw GitHub URLs).

---

## Description and sources

All IP lists are built from the following upstream projects. Credits and terms belong to them; this repo only combines and republishes for convenience.

| Category | Source | Description |
|----------|--------|-------------|
| **blocklist_de** | [blocklist.de](https://www.blocklist.de/en/export.html) | IPs reported by [fail2ban](https://www.fail2ban.org/) (SSH, Apache, Postfix, etc.) in the last 48 hours. |
| **firehol** | [firehol/blocklist-ipsets](https://github.com/firehol/blocklist-ipsets) | Aggregated blocklists: all `.ipset` files from the repo (abuse, attacks, botnets, etc.). |
| **bitwire** | [bitwire-it/ipblocklist](https://github.com/bitwire-it/ipblocklist) | Inbound malicious IP list (`inbound.txt`). |
| **cybercrime** | [abuse.ch Feodo Tracker](https://feodotracker.abuse.ch/downloads/ipblocklist.txt), [cybercrime-tracker.net](http://cybercrime-tracker.net/fuckerz.php), [firehol cybercrime.ipset](https://raw.githubusercontent.com/firehol/blocklist-ipsets/master/cybercrime.ipset) | Botnet C2 and cybercrime-related IPs from multiple trackers. |

---

## Integrated projects and list categories

The script produces one list per category (plus a combined CIDR list). All lists are reviewed so that the **proper categories of IPs are created as expected**:

| Output list | Contents |
|-------------|----------|
| `blocklist_de_ips_<ddmmyyyy>.txt` | Unique IPv4s from blocklist.de (fail2ban-style attackers). |
| `firehol_ips_<ddmmyyyy>.txt` | Unique IPv4s from every firehol blocklist-ipset (broad threat coverage). |
| `bitwire_ips_<ddmmyyyy>.txt` | Unique IPv4s from bitwire inbound list. |
| `cybercrime_ips_<ddmmyyyy>.txt` | Unique IPv4s from abuse.ch Feodo, cybercrime-tracker.net, and firehol cybercrime.ipset (C2 / cybercrime focus). |
| `combined_ranges_<ddmmyyyy>.txt` | Minimal set of CIDR ranges covering all unique IPs from the above; use this for efficient range-based blocking. |

Naming format: **`<source>_<category>_<ddmmyyyy>_<extras_if_required>.txt`**. Dates are in day-month-year so each run produces clearly dated files.

---

## Usage

1. **Install dependencies:** `pip install -r requirements.txt`
2. **Run:** `python3 pull_malicious_ips.py`

The script will:

- Clean old `.txt` list files from `lists/`
- Fetch from all sources above
- Deduplicate per source
- Write one file per category under `lists/` with the naming above
- Build the combined CIDR list
- `git add` the lists, commit, and push to the remote

**Consuming the lists:** Use the raw GitHub URLs (e.g. `https://raw.githubusercontent.com/<user>/Malicious_IP_Crawler/main/lists/blocklist_de_ips_28022026.txt`) in your firewall, WAF, or blocking tool. Replace the date with the file you want to use.
