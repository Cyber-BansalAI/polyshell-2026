# Hunt guide: did PolyShell reach our store?

As of **2026-10-28**. Read [DISCLAIMER.md](../DISCLAIMER.md) first. We have not tested these commands on your systems.
Run them yourself, or hand them to your developer or hosting company, and adjust paths to your setup. Nothing in this
package runs anything for you, and every command shown only reads.

This guide is for stores on **Adobe Commerce or Magento Open Source**. If your store runs on other software, the sources
we read do not say it is affected.

**Two questions, kept apart.**

1. *Is the fix on our store?* Answered by whoever runs the store, from Adobe's bulletin and support article (linked in
   [CREDITS.md](../CREDITS.md)).
2. *Did anything get in before the fix, or since?* Answered from the files on the server, the web server's logs and the
   store's page content. This guide covers that part.

Sansec reports that an uploaded file stays on the server even when the web server refuses to run it today. So fixing
the flaw does not answer the second question. Check the files too.

## 0. Set your window

- Sansec reports scanning from **late March 2026** and attacks from the same month. Search at least that far back if
  your logs go that far.
- If your logs begin later, say so in your notes: you cannot rule anything out for the earlier period.
- Ask when the fix went on. Adobe's own dates differ by version (see the blog post below and Adobe's bulletin).

## 1. Read the cautions first

- Some file names in the dataset are ordinary names (`index.php`, `test.php`, `c.php`). On its own, a match on the name
  proves little. It matters when the file sits in the store's upload folder, which should hold images and documents.
- The scanning addresses were seen in March 2026. Cloud addresses change hands. Several belong to large networks that
  many ordinary sites share. The rows say which. Look each one up before you block it.
- A clean result is not proof of safety. File names and addresses are easy to change.

## 2. Files in the upload folder

Sansec says files uploaded through the flaw land in the store's upload folder (`pub/media/custom_options/`, in a
subfolder), and that any script file there (`.php`, `.phtml` or `.phar`) is a strong sign of compromise. From the store's
root folder, this lists such files without changing anything:

```
find pub/media/custom_options -type f \( -name '*.php' -o -name '*.phtml' -o -name '*.phar' \) -print
```

Also list everything in that folder that is not an ordinary image or document, because attackers also disguise scripts:

```
find pub/media/custom_options -type f ! \( -name '*.jpg' -o -name '*.jpeg' -o -name '*.png' -o -name '*.gif' -o -name '*.pdf' -o -name '*.txt' \) -print
```

How to read it:

- **Any script file in that folder deserves a look.** The folder also holds ordinary customer uploads, so look at the
  file types, not the folder.
- Sansec says some uploads are valid images that also contain script code. A file named like an image can still be a
  problem. Your developer can check whether a file starts like an image and also holds code.
- Do not open, run or delete a file you find. Preserve it first (section 8).

## 3. The second-stage back door

Sansec says the attackers' back door file is named `accesson.php`, and that after a takeover it is copied into an
`assets/images` folder inside many of the store's top-level folders, so that one copy survives a cleanup. The dataset
lists the ten folders Sansec named. This searches the whole store folder for it:

```
find . -type f -name accesson.php -print
```

Then look for the other file names Sansec saw in the wild. The generic names (`index.php`, `c.php`, `r.php`,
`static.php`, `test.php`) are left out of this command, because they exist in many stores. This one is for the rest:

```
find . -type f \( -name 'json-shell.php' -o -name 'bypass.phtml' -o -name 'rce.php' -o -name 'blocked-json.php' -o -name 'bypass-async.php' -o -name 'urlencode-shell.php' -o -name 'xx_malicious_file.php' -o -name 'ato_poc.html' -o -name 'mikhail.html' -o -name 'toggige-arrow.jpg' -o -name 'adman.429.txt' -o -name 'adman.309.txt' -o -name '[0-9]*index.php' \) -print
```

The last pattern is a number followed by `index.php` (for example a shop product option number in front of the name).
Sansec calls it the most common pattern. Our note, not Sansec's: in the upload folder a match is a strong reason to
look, and elsewhere in the store, check the date and who created the file.

## 4. Page content

Sansec describes a two-stage script added to pages that loads code from an outside site. It says it can sit in the
store's content pages or static blocks. Three sites are named in the dataset (rows of type `domain`). This asks the
store's database whether any content mentions them (your developer or host runs it, with the store's normal read access):

```
SELECT page_id, identifier FROM cms_page WHERE content LIKE '%lanhd6549tdhse.top%' OR content LIKE '%jslibrary.net%' OR content LIKE '%canevaslab.com%';
SELECT block_id, identifier FROM cms_block WHERE content LIKE '%lanhd6549tdhse.top%' OR content LIKE '%jslibrary.net%' OR content LIKE '%canevaslab.com%';
```

How to read it:

- A page or block that mentions one of these sites is a strong reason to look closer. Do not visit the sites.
- Sansec notes that blocking the outside site does not clear copies of the script already saved in visitors' browsers.
- Nothing found here does not clear the store: the script can also live in a theme file or elsewhere.

## 5. Web server logs

Search your web server's logs for the scanning addresses. This reads the addresses from the dataset and checks the
current and the compressed older logs (adjust the log path to your server):

```
zgrep -F -f <(awk -F, '$3=="ip" {print $4}' iocs/iocs.csv) /var/log/nginx/access.log*
```

How to read it:

- A match is a visit from an address Sansec listed in March 2026. It is a reason to look at **what was requested** and
  **when**, not a finding by itself. Several of these addresses belong to networks many sites use.
- Ordinary scanning is common. The question is whether a request was followed by a new file in the upload folder at the
  same time (section 2).
- Sansec describes the attack in detail, including the request paths. We do not repeat them here (see
  [CREDITS.md](../CREDITS.md)). Request bodies are often not logged, so the logs may show only unusual cart requests.

## 6. Is the fix on?

This is the most useful check, and it is not a search. Ask whoever runs the store:

- Which version are we on?
- Has the July 14 fix for the supported versions been applied? Adobe's bulletin is APSB26-73. Adobe's support article
  says older versions got the fix as a separate patch, and that it is not easy to confirm it is in place.
- If we were open between March and the fix, did anyone check for files like the ones above?

## 7. What the results mean

| You found | It suggests | Next step |
|---|---|---|
| A published address in the web server log, nothing else | A scan, or a shared address | Look up the address, check the date and the request. Keep the logs |
| An ordinary name such as `index.php` somewhere in the store's code | Most likely a normal file | Check it is where it should be and that your developer recognises it |
| A script file in the upload folder | A possible upload through the flaw | Treat as an incident (section 8) |
| `accesson.php`, or another listed shell name | Likely attacker tooling | Treat as an incident |
| A page or block that mentions one of the three sites | Likely injected script | Treat as an incident |
| Nothing | Not proof of safety: these indicators change and your logs may not capture what matters | Confirm the fix is on (section 6) |

## 8. If you think it is real

Preserve first, clean later:

1. Do not delete the files yet. Copy the suspicious files and the logs somewhere safe, or ask your host to take a
   snapshot, so the evidence survives.
2. Call whoever runs the store and your hosting company. Do not use the affected server to talk about it.
3. Plan to change the passwords and keys the store uses (admin accounts, the database, payment and shipping
   connections) once the way in is closed, and to review what the store sent to customers. Your developer knows
   which ones exist.
4. Ask your payment provider and your insurance broker what they need to hear and how soon.

## Not sure what you found?

Bring the file names and log lines to your developer or host first. If you want a second pair of eyes,
[SOC-Lite by BansalAI](https://cyber.bansalai.com/?ref=github) offers a free 20-minute
[visibility check](https://cyber.bansalai.com/?ref=github#book). For the business-owner version of this page, with four
questions to ask whoever runs your store, read
[Online Store on Adobe Commerce or Magento? Ask About This Flaw](https://blog.bansalai.com/news/adobe-commerce-magento-store-owner-questions/?ref=github).

## Source for this guide

Sansec's article (full credit and link in [CREDITS.md](../CREDITS.md)) for the indicators and for what each means. The
search commands are ours, not Sansec's.
