# Wireshark 多協定封包分析專案

## 1. 專案目標
本專案使用 Wireshark 擷取並分析多種網路協定的封包，包含 HTTPS、HTTP、FTP 與 Telnet。透過實際觀察各協定封包內容，了解不同協定在安全性與資料傳輸方式上的差異。

## 2. 測試環境
- 作業系統：Windows / Linux / macOS
- 工具：Wireshark、瀏覽器、ftp server（可本地建置或使用 public server）、telnet client

## 3. 測試內容
| 協定  | 測試內容                  | 測試網址或工具                         |
|-------|---------------------------|----------------------------------------|
| HTTPS | 登入或瀏覽加密網站         | https://example.com                    |
| HTTP  | 兩個普通網站（可觀察 GET/POST） | http://neverssl.com, http://httpbin.org |
| FTP   | 登入 FTP 並上傳/下載檔案     | 本地 ftp server 或 public 測試 ftp     |
| Telnet| 登入遠端主機並傳送指令       | 本地 telnet server 或 `telnet towel.blinkenlights.nl` |

> 📌 相關擷取封包與截圖皆放於 `/captures/` 與 `/images/` 資料夾。

## 4. 封包分析摘要（更多細節見 report.md）
- **HTTPS** 封包內容為加密格式，無法看到帳號密碼。
- **HTTP** 封包中可清楚看到網址、參數與表單送出資料（有資安風險）。
- **FTP** 登入過程中帳號密碼以明文傳送。
- **Telnet** 全部資料皆為明文（包含指令與回應內容）。

## 5. 心得與收穫
透過這次實作，更加了解不同協定的安全風險與封包結構。尤其 FTP 與 Telnet 雖然簡單易用，但因為傳輸內容未加密，不建議在公開網路中使用。
