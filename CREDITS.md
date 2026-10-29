# Credits and sources

The indicators in this package come from one public research article, written by the team below. **We thank them for
publishing it.** Each indicator in `iocs/iocs.csv` names the source it came from. Being listed here does not mean any of
them reviewed, tested or endorsed this package.

We copy facts (indicator values) with attribution, and link to everything else. Where the article holds more than we
reproduce, follow the link for the full, current version.

## The research

| Who | What we used | Link |
|---|---|---|
| **PolyShell: unrestricted file upload in Magento and Adobe Commerce**, published 2026-03-17 and updated since, by the **Sansec Forensics Team** at **Sansec** | The lists of scanning addresses, injected-script sites, file names and paths seen in attacks, the two check values and the number the back door prints, and the descriptions of how attacks look that our notes and hunt guide rely on | https://sansec.io/research/magento-polyshell |

Sansec sells protection for online stores. The article's figures describe the stores it protects.

## Context sources (linked, nothing copied from them)

These do not list indicators. We read them for the timeline and the fix, and the hunt guide points to them.

- **CISA**, alert "CISA Adds Two Known Exploited Vulnerabilities to Catalog" (2026-10-27):
  https://www.cisa.gov/news-events/alerts/2026/10/27/cisa-adds-two-known-exploited-vulnerabilities-catalog
- **CISA**, Known Exploited Vulnerabilities Catalog: https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- **Adobe**, "Security update available for Adobe Commerce" (bulletin APSB26-73):
  https://www.adobe.com/trust/security/products/commerce/apsb26-73.html
- **Adobe Experience League**, "Security update available for Adobe Commerce" (support article):
  https://experienceleague.adobe.com/en/docs/experience-cloud-kcs/kbarticles/ka-37421
- **GitHub Advisory Database**, public advisory for the flaw: https://github.com/advisories/GHSA-p3ch-8535-fp8j

## Address registries

Our notes on who an address is registered to come from our own lookups on 2026-10-28 in the public registries of the
Regional Internet Registries (ARIN, RIPE NCC and APNIC) through their RDAP services. We name networks and organisations
only, not the people in contact fields.

## Further detail published by the same article (linked, not reproduced)

- The article's **code fragments** from the shells, the **request layout** of the attack, the commands it suggests for
  finding files, and its **web server rules**. We leave these out on purpose.
