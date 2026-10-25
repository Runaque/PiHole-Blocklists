# PiHole-Blocklists

Just my collection of blocklists for the Pi-hole.

Focused, hand-curated lists for things the big general-purpose lists tend to miss or handle too broadly: smart TV tracking (ACR), LG and Xiaomi devices, and ads in mobile apps and games. Every entry is commented so you can see what it blocks and what it might break.

## The lists

| List | What it blocks | Risk of breakage |
|---|---|---|
| [`acr-blocklist.txt`](acr-blocklist.txt) | Smart TV ACR (Automatic Content Recognition) and viewing-data collection: Samsung, LG, Vizio, Hisense, Roku, and Samba TV / Alphonso across brands | Low |
| [`acr-blocklist-aggressive.txt`](acr-blocklist-aggressive.txt) | Add-on to the above: broader Samsung and LG telemetry, remote management and firmware update servers | **High**, see below |
| [`lg-blocklist.txt`](lg-blocklist.txt) | LG webOS ads, Live Plus ACR and ad tracking. Optional telemetry entries included but commented out | Low |
| [`xiaomi-blocklist.txt`](xiaomi-blocklist.txt) | Xiaomi ads (MSA), analytics and tracking on MIUI / HyperOS phones and Mi Box devices | Low |
| [`google-ads-mobile-blocklist.txt`](google-ads-mobile-blocklist.txt) | Google ads (AdMob, DoubleClick, AdSense), Google / Firebase analytics, and Apple ad and analytics endpoints | Low to medium |
| [`android-game-ads-blocklist.txt`](android-game-ads-blocklist.txt) | Third-party ad networks used in mobile games: Unity Ads, AppLovin, ironSource, Liftoff/Vungle, Mintegral, Pangle and others | Medium |

## How to use

1. In the Pi-hole web interface, go to **Lists**.
2. Add the raw URL of each list you want, for example:
   ```
   https://raw.githubusercontent.com/Runaque/PiHole-Blocklists/main/acr-blocklist.txt
   ```
3. Run `pihole -g` (or **Tools > Update Gravity**) to load them.

All raw URLs follow the same pattern:

```
https://raw.githubusercontent.com/Runaque/PiHole-Blocklists/main/<list-name>.txt
```

## Format

The lists use Adblock-style syntax (`||example.com^`), supported by Pi-hole v6. One line blocks a domain **and all its subdomains**, so a single entry like `||lgsmartad.com^` covers `us.info.lgsmartad.com`, `de.ad.lgsmartad.com` and any new regional host the vendor adds later. Lines starting with `!` are comments.

## Things to know

- **`acr-blocklist-aggressive.txt` will break things.** It can stop app stores, built-in apps, Samsung TV Plus and firmware updates from working. Use it on top of the core list, and preferably assign it only to your TV's client group in Pi-hole rather than your whole network.
- **`android-game-ads-blocklist.txt` changes how games behave.** "Watch an ad for a reward" buttons stop working, and a few games may refuse to start when their ad SDK can't connect. A separate Pi-hole group for the devices that need it works best. It doesn't include Google AdMob; use it together with `google-ads-mobile-blocklist.txt` for that.
- **Xiaomi uses rotating regional tracking hosts.** For full coverage, also add this regex to Pi-hole's deny list (**Domains > Regex filter**), since adlists can't hold regex:
  ```
  ^tracking\..+\.miui\.com$
  ```
- **Devices can bypass your Pi-hole.** Many smart TVs fall back to hardcoded DNS servers (8.8.8.8, 1.1.1.1) or DNS-over-HTTPS when queries fail. Redirect outbound port 53 to your Pi-hole at the router and block port 853, or these lists only catch part of the traffic.
- **DNS blocking has limits.** YouTube ads and ads served from the same domain as the content itself can't be blocked this way.
- **Turn tracking off on the device too.** Disable ACR in the TV's own privacy settings (Samsung: Viewing Information Services, LG: Live Plus, Sony/Philips/TCL: Samba Interactive TV, Roku: Use info from TV inputs). The lists are a second layer, not a replacement.

## Something broke, or something's missing?

Check the Pi-hole query log filtered on the affected device to see which domain was blocked, then allowlist that specific domain rather than removing a whole list. Vendors rename and add domains with firmware updates, so if you spot a new tracking or ad domain, issues and pull requests are welcome.

## License

[MIT](LICENSE)
