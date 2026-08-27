# Proxy Filter for Windows (代理過濾器)

**[English version](https://github.com/RynelHub/proxy-filter/blob/main/README_EN.md)**    
**[Русская версия](https://github.com/RynelHub/proxy-filter/blob/main/README.md)**

**当前版本：** 2.1.0
*[点击此处下载最新版本](https://github.com/RynelHub/proxy-filter/releases/latest)*

*详细更新内容请查看 [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md)*

---

**Proxy Filter** 是一款综合工具，用于自动从公开订阅（subscriptions）中下载、处理、修正、过滤代理服务器配置，检测其可用性并进行分割。

在输入格式方面，Proxy Filter 支持 **YAML（json）** 和 **XRay（纯文本/base64）**。

---

## 1. 📥 输入数据

程序运行需要以下**两种**代理配置来源之一（取决于运行模式，详见下文）：
- `input_urls.txt` 文件，需要手动将订阅链接列表复制到该文件中（每行一个 URL）。
- 存放在 `temp` 文件夹中的、包含代理服务器配置的文本文件（.txt、.yaml、无扩展名文件等）。

**举例来说**，`input_urls.txt` 文件中已经添加了若干订阅链接示例。建议添加从第三方来源（Telegram、网络等）找到的其他订阅。

**此外**，为了正确判断所处理配置的地理归属，需要下载两个数据库（下载这些数据库需要在 Maxmind.com 网站上进行**免费注册**，注册链接为 https://www.maxmind.com/en/geolite2/signup）：
- https://download.maxmind.com/geoip/databases/GeoLite2-ASN/download?suffix=tar.gz
- https://download.maxmind.com/geoip/databases/GeoLite2-Country/download?suffix=tar.gz

建议定期更新上述数据库，以获得更准确的过滤结果。

Proxy Filter 要求地理数据库位于程序文件夹内的以下两个路径：
- {Proxy Filter folder}\geo_db\GeoLite2-ASN.mmdb
- {Proxy Filter folder}\geo_db\GeoLite2-Country.mmdb

**支持的订阅格式：**
- YAML、json
- 纯文本
- 纯文本，base64 编码

**Proxy Filter** 支持额外的自定义设置，使过滤过程更加灵活。
如需修改自定义设置，请在 **`config.ini`** 文件中设置相应参数的值。

---

## 2. 🧐 程序工作逻辑

**基本过滤规则：**
- **支持的协议：** VLESS、Trojan、Hysteria2、TUIC、Anytls。
- **端口：** 根据 `ONLY_443` 和 `DELETE_PORTS` 参数的值确定。
- **服务器地理归属：** 根据 `FILTER_MODE` 和 `COUNTRY_LIST` 参数的值确定。
- **去除重复**配置（不保证 100% 无重复）。
- 将最终配置数组**分割**为每个片段不超过 **N** 行配置：根据 `CHUNK_SIZE_WRITE` 参数的值确定。

Proxy Filter 运行结束后，会在 `output` 文件夹中生成文本文件，文件名格式为 **{protocol}_filtered--YYYY-MM-DD--HH-MM--{number}.txt**，其中包含过滤后的代理服务器配置。

---

## 3. ⚡ Proxy Filter 的运行模式
- `WORK_MODE = d` —— "DOWNLOAD"（下载），根据 `input_urls.txt` 中的链接下载文件（默认运行模式）。
- `WORK_MODE = n` —— "NO DOWNLOAD"（不下载），不下载文件，使用 `temp` 文件夹中的本地文件（此模式便于过滤自行下载/生成的配置文件）。

---

## 4. ☝🏻 过滤模式
- `FILTER_MODE = d` —— 根据 `COUNTRY_LIST` 参数中的掩码**删除**代理配置（如果您需要订阅中除某些配置外的全部配置），这是默认运行模式
- `FILTER_MODE = k` —— 根据 `COUNTRY_LIST` 参数中的掩码**保留**代理配置（如果您只需要订阅中的部分配置，其余的都不需要）

---

## 5. 🚀 **Proxy Filter** 的主要功能

### 源文件处理
- 通过 `input_urls.txt` 中的 URL 异步下载订阅文件。
- 随机化 User-Agent，并在重试下载之间设置随机延迟（模拟真实浏览器的行为）。

### 订阅文件的检查、修正与转换
- 自动修正下载文件的内容（包括编码问题、拆分"粘连"的行）。
- 跳过空文件和内容无效的文件。
- 将 YAML 格式转换为纯文本格式。
- 将 base64 格式转换为纯文本格式。

### 已下载配置的异步处理
- 根据设定标准排除不符合要求的配置。
- 解析代理配置中的域名，以确定其服务器 IP。
- 判定服务器的地理归属。
- 判定代理 IP/域名以及 SNI 所属的子网编号（ASN）及其所有者。
- 使用可变延迟测试配置的可用性（模拟"人类"行为）。
- 根据可用性测试结果，从最终列表中删除"失效"配置。
- 根据域名"黑名单"（`BLACKLIST_DOMAINS` 参数）删除相应配置。
- 自动修正配置，包括将"垃圾"域名替换为域名"白名单"（`WHITELIST_DOMAINS` 参数）中的正确域名。
- 为每个配置生成格式如下的信息性注释行：
 "{country code} | {latency}ms | {domain IP} | {domain AS number} | {domain AS name} || {sni} ({country code} | {sni IP} | {sni AS number} | {sni AS name}) || {current date}"。

### 缓存与统计
- 创建并使用本地缓存文件（`vpn_cache.json`），以判断下载的订阅文件是否为"最新"（内容自上次下载以来未发生变化的文件不会被重复处理）。
- 维护已检测配置的完整记录，并生成一份单独的、包含此前从未处理过的"新"配置的最终列表（文件名格式为 **new_filtered--YYYY-MM-DD--HH-MM--{number}.txt**）。
- 自动清理过期的缓存记录。
- 创建 `temp\_live_results.tmp` 文件，在 Proxy Filter 运行过程中实时更新，包含当前时刻的过滤结果，以防程序异常终止（防止已处理配置的丢失）。
- 每次程序运行结束后生成关于缓存状态的最终统计报告（文件名格式为 **cache_report_YYYY-MM-DD_HH-MM.csv**）。

### 日志记录
- 所有操作均记录在 `logs\app.log` 文件中。
- 支持日志轮转（最多保留 3 个文件，每个文件最大 1 MB）。

### 域名解析
- 支持多种 DNS 查询模式：DNS-over-HTTPS、公共、本地，具体根据 `DNS_MODE`、`DNS_DOH_URL` 和 `DNS_SERVER_IP` 参数的值确定。

### 支持通过本地系统代理服务器（127.0.0.1:{port}）工作
- 本地系统代理服务器的参数会自动确定，并在脚本开始运行前向用户显示确认提示。通过本地代理服务器工作可能会增加被测配置的响应时间。

### 执行时间统计
- **Proxy Filter** 运行结束后，会统计总处理时间。

---

## 6. 💡 使用说明

1a. **准备订阅 URL 文件：**
   - 打开 `input_urls.txt` 文件。
   - 添加订阅链接（以 `https://` 开头的行），每行一个。
   - 保存文件。
   - 在 `config.ini` 设置文件中，设置参数 `WORK_MODE = d`（使用订阅 URL 工作）和 `CLEAR_TEMP = 1`。

1b. **准备包含**支持格式的代理服务器配置行的文本文件：
   - 在程序的 `temp` 文件夹中创建 `test.txt` 文件（文件名可任意）。
   - 将从第三方来源找到的配置行（以 `vless://`、`trojan://` 等开头的行）复制到该文件中。
   - 保存文件。
   - 在 `config.ini` 设置文件中，设置参数 `WORK_MODE = n`（使用本地文件工作）和 `CLEAR_TEMP = 0`。

2. **如有需要，修改 `config.ini` 文件中的其他设置：**
   - 例如，可以指定用于按地理位置过滤最终代理列表的国家代码、端口、域名列表等。
   - 保存设置文件。

3. **运行程序：**
   - 双击 `proxy-filter.exe`。

4. **等待运行结束：**
   - 处理完成后，所有数据将出现在 `output` 文件夹中。

5. **包含程序运行详情的日志文件可在 `logs` 文件夹中找到**。

---

## 7. ⚠️ 特别说明

### 可用性检测
- Hysteria2、TUIC 和 Anytls 配置不会检测实际可用性，而是"原样"从订阅中过滤出来。在这些配置的注释行中，延迟数值处会显示 `?ms`，而非具体数值。

### 数据量
- 如果订阅中包含大量行（数万行），程序运行时间可能会**更长**。这属于正常现象。

### 请勿随意删除缓存文件
- 缓存可以加快 Proxy Filter 的运行速度，并为您节省原本用于测试已知无效配置所耗费的时间。
- 缓存的有效性会自动维护。

---

## 8. ✉️ 错误与反馈

本程序按"现状"提供。如果您遇到错误或异常行为：
- 请确认您使用的是 Microsoft Windows 操作系统（当前版本的 Proxy Filter 仅适用于该操作系统）。
- 检查 `input_urls.txt` 文件是否填写正确。
- 检查 `config.ini` 文件中的程序设置。
- 检查程序能否在本地正常访问 Maxmind 地理数据库。
- 关闭系统代理。
- 确认程序具有网络访问权限。
- 查看 `logs/app.log` 文件以获取更多信息。

如果仍然无法解决问题，您可以创建新的 **[Issue](https://github.com/RynelHub/proxy-filter/issues/new)**。

欢迎在 **[Discussions](https://github.com/RynelHub/proxy-filter/discussions/new)** 中留下反馈、评论、建议、批评及其他意见。

---

## 9. ❌ 免责声明

程序作者已尽最大努力确保程序稳定运行。但**不保证完全没有错误**。

---

## 10. ☕️ 支持本项目

朋友们，**Proxy Filter** 是一个非商业项目。这意味着该发行版完全免费提供，其开发完全出于作者的热情。

如果您喜欢这个项目，请为它点一个 **star** 以示支持！

您的支持将激励并帮助该项目（及作者）不断发展。
