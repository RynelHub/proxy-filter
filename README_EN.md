# Proxy Filter

**Current Version:** 1.2.0  
*[Download the latest version here](https://github.com/RynelHub/proxy-filter/releases/latest)*

*See [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md) for change details.*

---

**Proxy Filter** – A Windows utility for bulk filtering and conversion of **VLESS** and **Trojan** subscriptions.

## 📥 Input
- Subscription URLs in **Clash (YAML)** and **V2Ray (Base64/plain text)** formats.

## 📤 Output
- Text files containing **VLESS/Trojan** links, filtered by **TLS/Reality** and port **443**.
- Splits output into convenient parts for easy client import.

## 🛠 Features
- Supports filtering by **country codes** and **domains**.
- Processes several hundred subscriptions in **10-15 minutes**.

---

## 1. 🧐 What it was like before **Proxy Filter**...

You probably already performed these repetitive actions multiple times:

- Searching for proxy subscription links
- Downloading subscription files
- Decoding base64 (if the configurations were encoded)
- Converting YAML → V2Ray if you used a different client
- Fixing errors (wrong indentation, extra symbols, HTML tags)
- Filtering proxies by required parameters
- Copying needed configurations to a separate file for loading into the manager

😩 And this was happening **every few days**...

---

## 2. 🚀 What **Proxy Filter** Does?

**Proxy Filter** takes care of all the routine tasks!

💡 Now you only need to insert a subscription URL — the program does everything automatically:

- Automatically downloads and processes the files
- Decodes base64 if needed
- Fixes markup errors (quotes, indentation, incorrect symbols)
- Converts configurations between formats (YAML → V2Ray)
- Filters proxies based on your criteria
- Creates convenient files for further use

---

## 3. 💡 Why **Proxy Filter** is Convenient?

- Simplicity — you only need subscription links
- Speed — processing takes just minutes
- Automation — no manual actions needed!

⚡ Let **Proxy Filter** handle the routine work, while you focus on important tasks! 🏖

---

## 4. Input Data

The program requires a `input_urls.txt` file containing a list of subscription URLs (one URL per line).

What is a subscription?

A subscription is a webpage or file listing proxy server configurations (ranging from dozens to tens of thousands of entries). In the Chinese proxy community, these subscriptions are sometimes called “airports” (`机场`, pronounced "jīchǎng").

`input_urls.txt` contains **sample subscriptions** for reference, but they might be outdated or unavailable.

You will need to **add the latest subscriptions** found from other sources.

**Proxy Filter supports the following subscription formats:**
- Clash (YAML)
- V2Ray (plain text)
- V2Ray (base64 encoded)

---

## 5. Program Results

After processing the downloaded subscription files, text files containing filtered proxy configurations will appear in the `output` folder.

**Proxy Filter has the following fixed filtering settings:**
- **Supported protocols**: V

LESS, Trojan
- **Port**: 443
- **TLS (including TLS Reality) support**
- **Geographical proxy location**: according to the `COUNTRY_LIST` parameter in the `config.ini` file
- **Remove duplicate configurations** (without full guarantee of no duplicates in the final files).

Each output file will contain no more than **N** lines of configurations, based on the value of the `CHUNK_SIZE_WRITE` parameter in `config.ini`, formatted like:
- vless://<config string>
- trojan://<config string>

---

### 6. Main Functionality  

#### Loading Subscription Files  
- Multi-threaded downloading via URLs from the `input_urls.txt` file.  
- Randomization of User-Agent and delays between retry attempts.  

#### Processing and Fixing File Contents  
- Fixing file encoding if necessary.  
- Skipping empty files and files with incorrect content.  
- Automatic correction of errors in downloaded files (indentation, quotes, HTML tags, etc.).  

#### File Format Conversion  
- Converting data from YAML and BASE64 to plain text format.  

#### Customizable Filtering Mask for Proxy Configurations  
- Any domains and subdomains, including geographic filtering.  

#### Adjustable Maximum Number of Lines in Filtered Configuration Files  

#### Two Filtering Modes Available  
- **Exclusion mode (default):** Removes proxy configurations matching the `COUNTRY_LIST` mask (keeps all configurations except for the specified ones).  
- **Inclusion mode:** Keeps only the proxy configurations matching the `COUNTRY_LIST` mask (removes all others).  

#### Customizable Matching Scope for `COUNTRY_LIST` Mask  
- `servername`  
- `sni`  
- `host`  
- Comment line  

#### Data Filtering  
- Exclusion of unsuitable configurations based on specified rules.  
- Attempt to determine the geographic location of servers (including analysis of country flag emojis in comments).  

#### Removal of Duplicate Configurations  

#### Logging  
- All operations are recorded in the `logs/app.log` file.  
- Log rotation is supported (up to 3 files, each with a maximum size of 5 MB).  

#### Execution Time Calculation  
- Displays the total filtering time at the end of the process.

---

## 7. Usage Instructions

1. **Prepare the subscription URL file:**
   - Open `input_urls.txt`.
   - Add one subscription URL per line.
   - Save the file.

2. **If you don't want to use default settings, modify `config.ini`:**
   - Specify country codes or custom domains to filter the proxy list.
   - Modify the filtering mode.
   - Adjust match rules.
   - Save the file.

3. **Run the program:**
   - Double-click `proxy-filter.exe`.

4. **Wait for processing to finish:**
   - After processing, filtered configuration files will appear in the `output` folder.

---

## 8. Special Notes

### Data Volume
- If the subscriptions contain large amounts of data (tens of thousands of lines), the program may take **longer** to process. This is normal.

### Server Availability Check
- The program does **not check** server availability. Use external tools for this.

### Proxy Usage
- The program **does not support** system proxies. Please disable them before use.

---

## 9. Errors and Feedback

If you encounter issues:
- Ensure you're using Microsoft Windows (the current version of Proxy Filter is designed for this OS only).
- Check `input_urls.txt`.
- Verify `config.ini` settings.
- Ensure the program has network access.
- Check the `logs/app.log` file for more information.

If issues persist, submit an **[Issue](https://github.com/RynelHub/proxy-filter/issues/new)**.

---

## 10. Project Support

**Proxy Filter** is a non-commercial project, free for use, and developed with the author's passion.

If you enjoy this project, support it!

---

### [💰 Support Proxy Filter](https://rynelhub.github.io/donations/)

---

## 11. Disclaimer

The author makes every effort to ensure the program's stability, but **no guarantee is made** for error-free operation.
