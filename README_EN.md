# **Proxy Filter for Windows**                           

**[Русская версия](https://github.com/RynelHub/proxy-filter/blob/main/README.md)**      
**[中文版](https://github.com/RynelHub/proxy-filter/blob/main/README_CN.md)**

**Current version:** 2.1.0      
*[Download the latest version here](https://github.com/RynelHub/proxy-filter/releases/latest)*

*See [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md) for change details*

---

**Proxy Filter** is a combined tool for automatically downloading, processing, correcting, filtering, checking availability, and splitting proxy server configurations from publicly available subscriptions.

As input formats, Proxy Filter supports **YAML (json)** and **XRay (plain text/base64)**.

---

## 1. 📥 Input Data

To run the program, you need **one of two** sources of proxy server configurations (depending on the mode of operation, see below):
- an `input_urls.txt` file, into which you need to manually copy the list of subscription URLs (one URL per line).
- text files containing proxy server configurations (.txt, .yaml, files without extension, etc.), placed in the `temp` folder.

**As an example**, the `input_urls.txt` file already contains several subscription URLs. It is recommended to add additional subscriptions found in third-party sources (Telegram, the web, etc.).

**Additionally**, you need to download two databases for correctly determining the geographic location of the processed configurations (downloading these databases requires **free registration** on the Maxmind.com website at the link https://www.maxmind.com/en/geolite2/signup):
- https://download.maxmind.com/geoip/databases/GeoLite2-ASN/download?suffix=tar.gz
- https://download.maxmind.com/geoip/databases/GeoLite2-Country/download?suffix=tar.gz

It is recommended to regularly update these databases to get more relevant filtering results.

Proxy Filter expects the geo-databases to be available at the following two paths inside the program folder:
- {Proxy Filter folder}\geo_db\GeoLite2-ASN.mmdb
- {Proxy Filter folder}\geo_db\GeoLite2-Country.mmdb

**Supported subscription formats:**
- YAML, json
- plain text
- plain text, base64 encoded

**Proxy Filter** supports additional custom settings that make the filtering process more flexible.
To change custom settings, set the values of the corresponding parameters in the **`config.ini`** file.

---

## 2. 🧐 Program Logic

**Basic filtering rules:**
- **Supported protocols:** VLESS, Trojan, Hysteria2, TUIC, Anytls.
- **Ports:** according to the values of the `ONLY_443` and `DELETE_PORTS` parameters.
- **Server geographic location:** according to the values of the `FILTER_MODE` and `COUNTRY_LIST` parameters.
- **Duplicate removal** of configurations (100% absence of duplicates is not guaranteed).
- **Splitting** the final array of configurations into fragments containing no more than **N** configuration lines: according to the value of the `CHUNK_SIZE_WRITE` parameter.

After Proxy Filter finishes running, text files are created in the `output` folder with names in the format **{protocol}_filtered--YYYY-MM-DD--HH-MM--{number}.txt**, containing the filtered proxy server configurations.

---

## 3. ⚡ Proxy Filter Operating Modes
- `WORK_MODE = d` - "DOWNLOAD", downloading files from the links in `input_urls.txt` (default mode).
- `WORK_MODE = n` - "NO DOWNLOAD", don't download files, use local files from the `temp` folder (this mode is convenient for filtering files with configurations that you downloaded/created yourself).

---

## 4. ☝🏻 Filtering Modes
- `FILTER_MODE = d` - Remove proxy configurations matching the mask from the `COUNTRY_LIST` parameter (if you need all configurations from the subscription except some), this is the default mode
- `FILTER_MODE = k` - Keep proxy configurations matching the mask from the `COUNTRY_LIST` parameter (if you only need some configurations from the subscription, and don't need all the rest)

---

## 5. 🚀 Main Features of **Proxy Filter**

### Processing source files
- Asynchronous downloading of subscription files by URL from the `input_urls.txt` file.
- Randomization of the User-Agent and random delay between retry attempts (masking as the behavior of real browsers).

### Checking, fixing and converting subscription files
- Automatic correction of downloaded file content (including encoding, splitting of "glued" lines).
- Skipping empty files and files with invalid content.
- Conversion from YAML format to plain text format.
- Conversion from base64 format to plain text format.

### Asynchronous processing of downloaded configurations
- Exclusion of unsuitable configurations according to specified criteria.
- Resolving domains from proxy configurations to determine their server IPs.
- Determining the geographic location of servers.
- Determining the subnet number (ASN) and its owner for the proxy's IP/domain and for the SNI.
- Testing configuration availability using variable delay (imitating "human" behavior).
- Removing "dead" configurations from the final list based on availability test results.
- Removing configurations according to the domain "blacklist" (the `BLACKLIST_DOMAINS` parameter).
- Automatic correction of configurations, including replacing "junk" domains with correct ones from the domain "whitelist" (the `WHITELIST_DOMAINS` parameter).
- Generating an informative comment line for each configuration in the format:
 "{country code} | {latency}ms | {domain IP} | {domain AS number} | {domain AS name} || {sni} ({country code} | {sni IP} | {sni AS number} | {sni AS name}) || {current date}".

### Caching and statistics
- Creating and working with a local cache file (`vpn_cache.json`) to determine the "freshness" of downloaded subscription files (files whose content has not changed since the last download are not processed again).
- Maintaining a full registry of checked configurations, and forming a separate final list of "new" configurations that have never been processed before (files named in the format **new_filtered--YYYY-MM-DD--HH-MM--{number}.txt**).
- Automatic cleanup of outdated cache entries.
- Creating a `temp\_live_results.tmp` file, updated in real time while Proxy Filter is running, containing the current filtering results, in case of an abnormal termination (protection against losing already processed configurations).
- Generating final statistical reports on the cache state after each program session (files named in the format **cache_report_YYYY-MM-DD_HH-MM.csv**).

### Logging
- All operations are recorded in the `logs\app.log` file.
- Log rotation is supported (maximum of 3 files up to 1 MB each).

### Domain resolution
- Support for several DNS query modes: DNS-over-HTTPS, public, local, according to the values of the `DNS_MODE`, `DNS_DOH_URL` and `DNS_SERVER_IP` parameters.

### Support for working through a local system proxy server (127.0.0.1:{port})
- The local system proxy server parameters are determined automatically, with a confirmation prompt shown to the user before the script starts running. Working through a local proxy server may increase the response time of the tested configurations.

### Execution time counter
- At the end of **Proxy Filter**'s run, the total processing time is calculated.

---

## 6. 💡 Usage Instructions

1a. **Prepare the file with subscription URLs:**
   - Open the `input_urls.txt` file.
   - Add subscription links (lines starting with `https://`), one per line.
   - Save the file.
   - In the `config.ini` settings file, set the parameter `WORK_MODE = d` (work with subscription URLs) and `CLEAR_TEMP = 1`.

1b. **Prepare text file(s) containing** proxy server configuration lines in supported formats:
   - In the program's `temp` folder, create a `test.txt` file (any name works).
   - Copy the configuration lines found in third-party sources into this file (lines starting with `vless://`, `trojan://`, etc.).
   - Save the file.
   - In the `config.ini` settings file, set the parameter `WORK_MODE = n` (work with local files) and `CLEAR_TEMP = 0`.

2. **Change other settings in the `config.ini` file, if needed:**
   - For example, you can specify country codes for filtering the final proxy list by geographic location, ports, domain lists, etc.
   - Save the settings file.

3. **Run the program:**
   - Double-click `proxy-filter.exe`.

4. **Wait for it to finish:**
   - After processing, all data will appear in the `output` folder.

5. **A log file with details of the program's operation can be found in the `logs` folder**.

---

## 7. ⚠️ Special Notes

### Availability check
- Hysteria2, TUIC and Anytls configurations are not checked for actual availability, and are filtered from subscriptions "as is". Instead of a latency value, the comment lines for these configurations show `?ms`.

### Data volume
- If subscriptions contain a large number of lines (tens of thousands), the program may run for a **longer** time. This is normal.

### Don't delete the cache file unnecessarily
- The cache speeds up Proxy Filter's work, and saves you time that would otherwise be spent testing configurations that are already known to be invalid.
- Cache relevance is maintained automatically.

---

## 8. ✉️ Bugs and Feedback

The program is provided "as is". If you encounter bugs or unusual behavior:
- Make sure you are using the Microsoft Windows operating system (the current version of Proxy Filter is designed for this OS only).
- Check that the `input_urls.txt` file is filled in correctly.
- Check the program settings in the `config.ini` file.
- Check local availability of the Maxmind geo-databases for the program.
- Disable the system proxy.
- Make sure the program has network access.
- Check the `logs/app.log` file for additional information.

If nothing works, you can create a new **[Issue](https://github.com/RynelHub/proxy-filter/issues/new)**.

Leave feedback, comments, suggestions, criticism and other feedback in **[Discussions](https://github.com/RynelHub/proxy-filter/discussions/new)**.

---

## 9. ❌ Disclaimer

The author of the program has made every effort to ensure its stable operation. However, **complete absence of errors is not guaranteed**.

---

## 10. ☕️ Support the Project

Friends, **Proxy Filter** is a non-commercial project. This means the distribution is completely free, and development is carried out on the author's enthusiasm.

If you like this project, support it with a **star**!

Your support motivates and helps the project (and the author) grow.

**Thank you for using Proxy Filter!**

### [💰 Here you can support **Proxy Filter** financially](https://rynelhub.github.io/donations/).
