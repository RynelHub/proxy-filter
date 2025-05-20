# Proxy Filter (代理過濾器)

**[English version](https://github.com/RynelHub/proxy-filter/blob/main/README_EN.md)**  
**[Русская версия](https://github.com/RynelHub/proxy-filter/blob/main/README.md)**  

**当前版本:** 1.2.1  
*[在此下载最新版本](https://github.com/RynelHub/proxy-filter/releases/latest)*  

*详细更新内容请查看 [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md)*  

---

**Proxy Filter** – 适用于 Windows 的 **VLESS** 和 **Trojan** 订阅批量筛选与转换工具。

## 📥 输入
- **Clash (YAML)** 和 **V2Ray (Base64/纯文本)** 格式的订阅链接。

## 📤 输出
- 筛选后的 **VLESS/Trojan** 链接（基于 **TLS/Reality** 和 **443 端口**）。
- 自动分割输出文件，方便客户端导入。

## 🛠 功能
- 支持按 **国家代码** 和 **域名** 进行筛选。
- 可在 **10-15 分钟** 内处理数百个订阅链接。

---

## 1. 🧐 在使用 **Proxy Filter** 之前...  

您可能已经多次手动执行以下繁琐操作：  

- 查找代理订阅链接  
- 通过这些链接下载订阅文件  
- 解码 base64（如果订阅文件是编码格式）  
- 转换 YAML → V2Ray，如果您使用的是 V2Ray 客户端  
- 修正错误（缩进错误、多余字符、HTML 代码等）  
- 按照特定规则筛选代理服务器  
- 复制所需的代理配置到单独的文件以供导入  

😩 而且 **每隔几天** 都要重复这些操作……  

---

## 2. 🚀 **Proxy Filter** 能做什么？  

**Proxy Filter** 让这一切变得自动化！  

💡 您只需要提供代理订阅的 URL，程序将自动完成以下任务：  

- 自动下载并解析订阅文件  
- 解码 base64（如果需要）  
- 修正格式错误（引号、缩进、不正确的符号等）  
- 在 **YAML** 和 **V2Ray** 之间转换格式  
- 按照您的筛选规则提取所需代理  
- 生成可直接使用的配置文件  

---

## 3. 💡 为什么 **Proxy Filter** 更方便？  

- **简单** — 只需提供订阅链接  
- **快速** — 处理只需几分钟  
- **全自动** — 无需手动干预  

⚡ 让 **Proxy Filter** 处理繁琐任务，您可以专注于更重要的事情！🏖  

---

## 4. 输入数据  

程序使用 `input_urls.txt` 文件，该文件包含代理订阅 URL（每行一个）。  

什么是 `订阅`（Subscription）？  

订阅是一种包含代理服务器配置信息的网页或文件（可能包含数十甚至数千条配置）。  
在中文社区，代理订阅通常被称为 **机场**（`机场`，发音为 "jī chǎng"）。  

`input_urls.txt` 文件包含示例订阅链接，但它们可能已失效或过时。  

您需要 **自行** 添加最新可用的订阅链接。  

**Proxy Filter 支持以下订阅格式：**  
- Clash (YAML)  
- V2Ray (纯文本)  
- V2Ray (base64 编码)  

---

## 5. 处理结果  

处理完成后，程序会在 `output` 文件夹中生成文本文件，包含筛选后的代理服务器配置。  

**Proxy Filter 的默认筛选规则包括：**  
- **支持的协议：** VLESS, Trojan  
- **端口号：** 443  
- **TLS（包括 TLS Reality）必须启用**  
- **地理位置过滤：** 根据 `config.ini` 文件中的 `COUNTRY_LIST` 参数  
- **去重处理**（不保证完全无重复）  

每个结果文件最多包含 **N** 条代理配置，具体数量由 `config.ini` 中的 `CHUNK_SIZE_WRITE` 设定。格式如下：  
- vless://<配置字符串>  
- trojan://<配置字符串>  

---

## 6. 主要功能  

### 订阅下载  
- 多线程下载 `input_urls.txt` 中的 URL  
- 随机化 User-Agent，并添加下载重试延迟  

### 文件解析与修正  
- 自动修正编码格式  
- 跳过空文件或格式错误的文件  
- 自动修正 YAML/V2Ray 语法错误（缩进、引号、HTML 标签等）  

### 格式转换  
- 支持 YAML 和 BASE64 到文本格式的转换  

### 代理筛选规则  
- 可根据 **域名、国家/地区** 进行筛选  

### 配置文件拆分  
- 可设置单个文件的最大行数  

### 两种筛选模式  
- **排除模式**（默认）：移除 `COUNTRY_LIST` 中的国家/地区代理  
- **保留模式**：仅保留 `COUNTRY_LIST` 中的代理  

### 筛选匹配字段  
- servername  
- sni  
- host  
- 备注（comment）  

### 数据清理  
- 过滤无效代理  
- 基于国家/地区信息筛选（可解析 flag emoji）  

### 去重  
- 过滤重复代理配置  

### 日志记录  
- 详细日志存储在 `logs/app.log`  
- 日志轮换（最多保留 3 个日志文件，每个 5MB）  

### 运行时间统计  
- 处理完成后，显示总运行时间  

---

## 7. 使用指南  

1. **准备订阅链接文件**  
   - 打开 `input_urls.txt`  
   - 添加订阅链接（每行一个）  
   - 保存文件  

2. **修改配置（可选）**  
   - 在 `config.ini` 中调整筛选规则  
   - 设置国家/地区代码或域名筛选规则  
   - 选择筛选模式（排除/保留）  
   - 调整是否删除临时文件  
   - 保存文件  

3. **运行程序**  
   - 双击 `proxy-filter.exe`  

4. **等待程序完成处理**  
   - 处理完成后，`output` 文件夹将生成筛选后的代理配置  

5. **查看日志**  
   - 日志文件位于 `logs` 文件夹  

---

## 8. 重要说明  

### 处理大规模订阅  
- 如果订阅包含大量代理（数万条），处理时间会相对较长  

### 代理可用性检查  
- 该程序 **不会** 测试代理可用性，请使用其他工具进行检查  

### 代理访问  
- 目前 **不支持** 通过系统代理访问网络，请关闭代理后使用  

---

## 9. 问题反馈  

**Proxy Filter** 按 “原样” 提供，若遇到错误或异常情况，请检查：  
- 是否在 **Windows** 运行（当前版本仅支持 Windows）  
- `input_urls.txt` 是否正确填写  
- `config.ini` 是否设置正确  
- 关闭系统代理后重试  
- 程序是否具有网络访问权限  
- 检查 `logs/app.log` 以获取详细错误信息  

如果仍然有问题，欢迎提交 **[Issue](https://github.com/RynelHub/proxy-filter/issues/new)**。  

**意见、建议、反馈，请访问 [Discussions](https://github.com/RynelHub/proxy-filter/discussions/new)**。  

让我们共同改进 **Proxy Filter**！  

---

## 10. 项目支持  

**Proxy Filter** 是一个非商业项目，完全免费，开发者凭兴趣维护。  

如果您觉得这个项目有帮助，可以捐助支持！  

**[💰 赞助 Proxy Filter](https://rynelhub.github.io/donations/)**  

---

## 11. 免责声明  

本程序尽力保证稳定性，但 **不保证完全无错误**。  

