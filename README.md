# PolyShell (CVE-2026-48356): indicators and hunt guide for Adobe Commerce and Magento stores

> **Read first.** This package is aggregated from a public research article by SOC-Lite by BansalAI. It is not tested
> by us, it comes with no warranty, and it is not professional advice. It is as of **2026-10-28** and will go stale. See
> [DISCLAIMER.md](DISCLAIMER.md). We are not affiliated with Sansec or any other source credited in
> [CREDITS.md](CREDITS.md), and none of them reviewed or endorsed this package.

On 2026-10-27 the U.S. Cybersecurity and Infrastructure Security Agency (CISA) added **CVE-2026-48356**, nicknamed
**PolyShell**, to its Known Exploited Vulnerabilities catalog. The flaw is in Adobe Commerce and Magento Open Source,
the software behind some online stores. The security firm Sansec published a report on it on 2026-03-17 (and has updated
it since). The report says a visitor with no login can upload a file through the store's shopping cart, and that the file
can then be run on many stores. It also lists indicators of attacks that Sansec saw.

This repository copies **those indicators** (with credit) and adds a short guide to checking your own store for them.

**Not technical? Start with the plain-language version:**
[Online Store on Adobe Commerce or Magento? Ask About This Flaw](https://blog.bansalai.com/news/adobe-commerce-magento-store-owner-questions/?ref=github)
on the SOC-Lite blog. It gives four questions to put to whoever runs your store.

## What is in here

| File | What |
|---|---|
| [`iocs/iocs.csv`](iocs/iocs.csv) | One row per indicator, with the source, the date, how fragile it is, and a review date |
| [`docs/hunt-guide.md`](docs/hunt-guide.md) | What to check on a store's server, logs and page content, what the results do and do not mean, and what to do if something looks real |
| [`CREDITS.md`](CREDITS.md) | The research this is built from, and what we took from it |
| [`DISCLAIMER.md`](DISCLAIMER.md) | The full disclaimer |

## Read the results carefully

- **A match is a reason to look, not a verdict.** Some listed file names are ordinary names (`index.php`, `test.php`).
  They matter when they sit where they should not, such as the store's upload folder. The rows say which.
- **Do not mass-block addresses.** The 42 scanning addresses were seen in March 2026, so some have changed hands since.
  Our own check of the address registries (October 2026) shows several belong to large cloud and delivery networks that
  many ordinary sites share. The rows say which. Look each address up against your own logs first.
- **Sansec sells protection for online stores.** Its figures describe the stores it protects, not the whole market.
  Read its report with that in mind.
- **The report is one source.** Every row comes from it, so no row is marked verified. We mark a row verified only when
  two independent sources report it.
- **Two details on the page look off,** and the rows say so. The list is headed "50+" addresses but holds 42, and one
  check value has 42 characters, which matches no standard hash length. We recorded both as written.
- **These indicators go stale quickly.** File names and addresses are cheap for an attacker to change, so a clean result
  is not an all-clear. Fixing the flaw does not remove files already uploaded.
- **We left out the attack details on purpose.** The report shows the shells' code and the shape of the cart requests.
  We do not copy them. Follow the link in [CREDITS.md](CREDITS.md) if you need them.

## Using `iocs.csv`

| Column | Meaning |
|---|---|
| `id` | Stable row ID (`PS-nnnn`) |
| `wave` | Always `polyshell` in this release |
| `type` | `ip`, `domain`, `file-path`, `filename`, `string` or `behaviour` |
| `value` | The indicator. Addresses and domains are written as normal text, not defanged. Never open them in a browser |
| `match` | `exact`, `prefix` or `wildcard` |
| `evidence` | Where it would show: `network`, `file`, `response-body` or `access-log` |
| `fragile` | `yes` if the attacker can change it trivially |
| `verified` | `yes` only when two independent primary sources report it. `no` means a single source |
| `source_url`, `source_date` | Where we took it from and the date of that source |
| `fetched`, `review_by` | When we read it, and when it should be re-checked. Addresses and domains have short review dates |
| `notes` | Context and caveats. Sentences starting "Our note" are ours, not Sansec's |

Search your own store with the steps in the hunt guide. This package does not ship anything that runs on your systems
or collects data from them.

## Licence

The text we wrote is licensed under CC BY 4.0 ([LICENSE](LICENSE)). The indicator values and facts belong to the
researchers who reported them: they are credited in [CREDITS.md](CREDITS.md), and we do not relicense them. Details and
how to credit us are in [NOTICE.md](NOTICE.md).

## Where to go for the full, current picture

Sansec's report is the source for everything here and may be updated. For its complete text, including the technical
detail we leave out, follow the link in [CREDITS.md](CREDITS.md). For the fix itself, read Adobe's security bulletin
and support article, linked there too.

## Help with a suspected compromise

If something looks real, preserve the logs first, take the store out of service if you can, and call whoever runs it
for you. [SOC-Lite by BansalAI](https://cyber.bansalai.com/?ref=github) helps small and mid-sized Central Florida
businesses review alerts and work with their IT person or MSP.
[Book a free 20-minute visibility check](https://cyber.bansalai.com/?ref=github#book). More plain-language security
write-ups are on the [SOC-Lite blog](https://blog.bansalai.com/?ref=github).
