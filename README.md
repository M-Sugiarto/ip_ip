# IP_IP — IP Intelligence Workbench

A single-file, browser-only OSINT dashboard for IP reputation, network, geolocation, DNS, and blacklist intelligence — no backend, no server, no tracking. Static HTML/CSS/JS, free to host on GitHub Pages[cite: 3].

[Live preview](https://m-sugiarto.github.io/ip_ip/) — open `index.html` directly in any browser, no build step, no server required[cite: 3].

**Repo:** [github.com/M-Sugiarto/ip_ip](https://github.com/M-Sugiarto/ip_ip)[cite: 3]  
**Author:** [github.com/M-Sugiarto](https://github.com/M-Sugiarto)[cite: 3]

---

## 🔒 Security & Hardening Disclosure

While this workbench is architected as a **Zero-Server, client-side application** running entirely within your browser sandbox, **local execution does not guarantee absolute immunity against compromise**. 

### Hardened Architecture Implemented
To minimize the attack surface, the project adopts these defensive design choices:
1. **Zero Backend / Zero Database:** No server-side components, session databases, or logging backends exist to be compromised or leaked.
2. **LocalStorage Isolation:** Optional API keys and cache history are restricted to the browser's local origin sandbox (`localStorage`) and sent *only* to the intended telemetry providers.
3. **No External Script Dependencies:** Core logic contains no third-party JavaScript libraries loaded from unpinned or untrusted CDNs (aside from standard Leaflet mapping resources)[cite: 3].
4. **Content Security Boundary:** Operates entirely within the browser's native sandboxed execution context.

### Known Security Limitations & Risks
* **Host Machine Compromise:** If your local machine or personal PC is infected with malware, keyloggers, or malicious browser extensions, the local files, cached sessions, and API keys stored in `localStorage` can be extracted or hijacked.
* **Metadata & OPSEC Leakage:** While this tool does not track you[cite: 3], **third-party API providers** (such as ipwho.is, AbuseIPDB, VirusTotal, and Cloudflare DoH) will log the target IPs you query and your source connection metadata. 
* **Client-Side Vulnerabilities:** DOM-based injection or logic manipulation remains a theoretical risk if malicious input vectors bypass strict escaping functions (`esc()`).

**Recommendation:** Run this tool on a clean, patched local environment or a dedicated user profile. Avoid exposing API keys with high privileges; utilize restricted free-tier tokens where possible.

---

## What has changed or been added from the previous version?

To maintain full transparency, here is the granular change log tracking every feature, bug fix, and security update integrated into the latest build:

### 1. Security & Hardening Disclosures Added
* **New Section:** Introduced a dedicated *Security & Hardening Disclosure* section in the documentation detailing the exact threat model, local execution risks (e.g., host compromise and API metadata leakage), and mitigation strategies.

### 2. Heuristic VPN & Proxy Classification Fixes
* **Resolved False Negatives:** Fixed an issue where popular VPN exit nodes (such as Surfshark IPs like `93.185.162.14`) were miscategorized as standard hosting providers or normal ISP connections because raw geolocation databases failed to set `security.vpn = true`.
* **Added Heuristic Rules:** Embedded robust organization and ASN pattern-matching checks within the UI rendering engine for major VPN/hosting providers (Surfshark, M247, NordVPN, ExpressVPN, etc.) to ensure accurate tagging.

### 3. Decoupled Abuser Logic (False Positive Fix)
* **Resolved False Positives:** Fixed an issue where clean VPN or proxy IPs were incorrectly flagged with an **Abuser** badge. 
* **Refined Rule Logic:** Decoupled security classifications so that an IP is only marked as an **Abuser** if there is empirical proof, such as positive DNSBL blacklisting or a high abuse confidence score, rather than automatically flagging every proxy or VPN.

### 4. Advanced Features & Panels Introduced
* **Infrastructure Pivot Graph:** Added a client-side OSINT relation mapping graph visualizing ASN neighbors, CIDR blocks, hostnames, certificate SANs from `crt.sh`, and threat feed correlations.
* **Multi-Source RSS Threat Feed Aggregator:** Added a live security threat feed pulling from 10 trusted cybersecurity sources via a parallel CORS-proxy race with built-in runtime telemetry diagnostics.

---

## Data sources

### Always on — no API key needed
| Panel | Source |
|---|---|
| Geolocation, ISP, VPN/Proxy/Tor/Hosting flags | [ipwho.is](https://ipwho.is)[cite: 3] |
| ASN / prefix / RIR allocation | [BGPView](https://bgpview.io) & RIPEstat[cite: 3] |
| Full BGP / peering report (external link) | [Hurricane Electric bgp.he.net](https://bgp.he.net)[cite: 3] |
| WHOIS-style record | [RDAP.org](https://rdap.org)[cite: 3] |
| Reverse DNS (PTR) | Cloudflare DNS-over-HTTPS[cite: 3] |
| DNSBL blacklist matrix — Spamhaus ZEN, SpamCop, Barracuda, SORBS, CBL | live DNS zone queries over Cloudflare DoH[cite: 3] |
| Open ports / hostnames / known CVEs | [Shodan InternetDB](https://internetdb.shodan.io)[cite: 3] |
| Live Security Threat Feeds | Multi-source CORS proxy race |

### Optional — bring your own free API key
| Panel | Source | Free key |
|---|---|---|
| Abuse confidence & report history | [AbuseIPDB](https://www.abuseipdb.com/api)[cite: 3] | [Register](https://www.abuseipdb.com/register)[cite: 3] |
| Vendor malicious-detection counts | [VirusTotal](https://www.virustotal.com)[cite: 3] | [Join](https://www.virustotal.com/gui/join-us)[cite: 3] |
| Internet-noise / scanner classification | [GreyNoise Community](https://www.greynoise.io)[cite: 3] | [Sign up](https://viz.greynoise.io/signup)[cite: 3] |
| Fraud score, proxy/VPN/bot indicators | [IPQualityScore](https://www.ipqualityscore.com)[cite: 3] | [Create account](https://www.ipqualityscore.com/create-account)[cite: 3] |
| Higher-rate-limit threat pulses | [AlienVault OTX](https://otx.alienvault.com)[cite: 3] | [Settings](https://otx.alienvault.com/settings)[cite: 3] |

Supply keys via the **Settings (⚙)** modal (saved to `localStorage`)[cite: 3] or hardcode them directly into the `DEFAULT_KEYS` object inside `index.html`.

## Features

- **Heuristically enhanced VPN/Proxy detection** ensuring accurate tagging for services like Surfshark and M247.
- **Infrastructure Pivot Graph:** Visualizes ASN relationships, prefix blocks, hostnames, and certificate SANs.
- **Multi-Source RSS Threat Feed Aggregator:** Live-fetches 10 trusted cybersecurity intelligence feeds via a parallel CORS-proxy race.
- **Client-side risk-scoring engine** combining DNSBL hits, VPN/proxy/Tor/hosting flags, exposed-service CVEs, and reputation provider scores[cite: 3].
- **Export capabilities:** Copy or download reports as JSON, CSV, or Markdown[cite: 3].

## Running it

Static site — nothing to build or install[cite: 3].

- **Locally:** open `index.html` directly in a browser, or serve the folder with any static file server (e.g., `python3 -m http.server`)[cite: 3].
- **GitHub Pages:** push this repo, then *Settings → Pages → Deploy from branch → main → / (root)*[cite: 3].

## License

MIT — see [LICENSE](LICENSE)[cite: 3].
