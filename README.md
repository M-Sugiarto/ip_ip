# IP_IP — IP Intelligence Workbench

A single-file, browser-only OSINT dashboard for IP reputation, network, geolocation, DNS, and blacklist intelligence — no backend, no server, no tracking. Static HTML/CSS/JS, free to host on GitHub Pages.

[Live preview](https://m-sugiarto.github.io/ip_ip/) — open `index.html` directly in any browser, no build step, no server required.

**Repo:** [github.com/M-Sugiarto/ip_ip](https://github.com/M-Sugiarto/ip_ip)
**Author:** [github.com/M-Sugiarto](https://github.com/M-Sugiarto)

## What it does

Enter an IP and it queries a set of free public data sources in parallel, aggregates the results, and computes a transparent 0–100 risk score with a visible breakdown of what contributed. While the scan runs, the risk gauge doubles as a glowing animated progress ring counting from 0–100% as each source resolves.

## Data sources

### Always on — no API key needed
| Panel | Source |
|---|---|
| Geolocation, ISP, VPN/Proxy/Tor/Hosting flags | [ipwho.is](https://ipwho.is) |
| ASN / prefix / RIR allocation | [BGPView](https://bgpview.io) |
| Full BGP / peering report (external link) | [Hurricane Electric bgp.he.net](https://bgp.he.net) |
| WHOIS-style record | [RDAP.org](https://rdap.org) |
| Reverse DNS (PTR) | Cloudflare DNS-over-HTTPS |
| DNSBL blacklist matrix — Spamhaus ZEN, SpamCop, Barracuda, SORBS, CBL | live DNS zone queries over Cloudflare DoH |
| Open ports / hostnames / known CVEs | [Shodan InternetDB](https://internetdb.shodan.io) |
| Threat pulse count (reduced rate without a key) | [AlienVault OTX](https://otx.alienvault.com) |

The DNSBL matrix is a genuine live check, not a mock table: it queries the reversed-IP subdomain of each blacklist zone over DoH and reads back whether an A record exists (listed) or NXDOMAIN comes back (clean) — the same mechanism traditional DNSBL clients use.

### Optional — bring your own free API key
| Panel | Source | Free key |
|---|---|---|
| Abuse confidence & report history | [AbuseIPDB](https://www.abuseipdb.com/api) | [Register](https://www.abuseipdb.com/register) |
| Vendor malicious-detection counts | [VirusTotal](https://www.virustotal.com) | [Join](https://www.virustotal.com/gui/join-us) |
| Internet-noise / scanner classification | [GreyNoise Community](https://www.greynoise.io) | [Sign up](https://viz.greynoise.io/signup) |
| Fraud score, proxy/VPN/bot indicators | [IPQualityScore](https://www.ipqualityscore.com) | [Create account](https://www.ipqualityscore.com/create-account) |
| Higher-rate-limit threat pulses | [AlienVault OTX](https://otx.alienvault.com) | [Settings](https://otx.alienvault.com/settings) |

There are two ways to supply a key:

1. **Via the GUI** — click the ⚙ Settings icon in the app, paste the key, save. It's stored only in your own browser's `localStorage` and sent directly to that provider's API.
2. **Baked into the code** — open `index.html`, find the `DEFAULT_KEYS` object near the top of the `<script>` block, and fill in a value there before deploying. Every visitor to your hosted copy then gets that data automatically with no setup on their end.

If a key isn't set either way, that panel shows **"Please set up your API key"** with a button that jumps straight to the relevant Settings field, instead of silently failing.

### Live API status monitor

A sticky status bar under the header shows a chip per key-gated provider — lit up (green dot) when a key is detected (from Settings or `DEFAULT_KEYS`), dim (red dot) when it isn't. Click any chip to jump straight to that provider's field in Settings.

## Features

- Client-side risk-scoring engine combining DNSBL hits, VPN/proxy/Tor/hosting flags, exposed-service CVEs, and any configured AbuseIPDB / VirusTotal / GreyNoise / IPQualityScore / OTX signals into one 0–100 score with a visible factor-by-factor breakdown.
- Animated scan-progress ring (0→100%, glowing) while sources resolve, which then morphs into the final risk gauge.
- Tabs: Network/ASN (with a direct link out to that IP's or ASN's full report on bgp.he.net), Geolocation (Leaflet/OpenStreetMap map), WHOIS/RDAP, DNS, Threat & Blacklists, Ports & Services, Raw JSON.
- Export as JSON, CSV, or a Markdown report (copy or download).
- LocalStorage caching with a configurable TTL so repeat lookups don't burn free-tier rate limits.
- Recent-lookup history, dark/light theme, fully responsive, keyboard-friendly.

## Running it

Static site — nothing to build or install.

- **Locally:** open `index.html` directly in a browser, or serve the folder with any static file server (e.g. `python3 -m http.server`).
- **GitHub Pages:** push this repo, then *Settings → Pages → Deploy from branch → main → / (root)*. Live at `https://m-sugiarto.github.io/ip_ip/`.

## Notes & limitations

- Some providers rate-limit or CORS-block anonymous/direct browser requests (VirusTotal and some AbuseIPDB tiers especially). When that happens the relevant panel says so explicitly rather than failing silently — it's a provider-side restriction, not a bug in the page.
- IPv6 support is partial: geolocation, ASN, WHOIS, ports, and most DNSBL zones work; PTR/forward DNS record expansion and OTX lookups for IPv6 are not implemented in this build.
- bgp.he.net does not offer a public JSON API, so it's wired up as a direct external link (auto-populated with the looked-up IP or ASN) rather than an embedded panel.
- This tool aggregates public OSINT data for defensive/research purposes (SOC triage, threat hunting, due diligence). It does not perform active scanning of the target IP — every data point comes from a third-party public database.

## License

MIT — see [LICENSE](LICENSE).
