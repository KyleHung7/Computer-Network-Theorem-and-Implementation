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



```
wireshark-multi-protocol-analysis/
├── README.md              # 專案總說明（下面會幫你寫初稿）
├── captures/              # 封包擷取結果
│   ├── https-site.pcap
│   ├── http-site1.pcap
│   ├── http-site2.pcap
│   ├── ftp-session.pcap
│   └── telnet-session.pcap
├── images/                # 封包截圖
│   ├── https-login.png
│   ├── http1-get.png
│   ├── http2-post.png
│   ├── ftp-login.png
│   └── telnet-command.png
└── report.md              # 詳細分析與心得（可用 Markdown 寫）
```


📷 截圖建議（images 資料夾內容）
圖檔名稱	說明
https-login.png	擷取 HTTPS 登入封包截圖（例如 TLS handshake）
http1-get.png	HTTP 瀏覽網站過程的 GET 請求封包截圖
http2-post.png	HTTP 表單登入 POST 請求封包截圖
ftp-login.png	FTP 登入時顯示帳密封包截圖
telnet-command.png	Telnet 中下指令與主機回應的封包截圖

📌 擷取技巧與觀察建議
協定	過濾語法（Wireshark）	可觀察內容
HTTPS	tcp.port == 443	TLS 握手過程（看不到帳密）
HTTP	http 或 http.request	URI、GET/POST 參數、User-Agent
FTP	ftp 或 ftp.request.command	USER、PASS、STOR、RETR 等命令
Telnet	telnet 或 tcp.port == 23	使用者輸入的文字、伺服器回應
❓你接下來可以做什麼：
按這份架構照著操作一次並保存 .pcap 和截圖

把圖片和檔案放上 GitHub

把每個協定的分析與心得補到 report.md 中




# Traceroute 分析報告

## 📌 封包說明與類型整理
### cmd 輸入 tracert www.ntnu.edu.tw
![image](https://github.com/user-attachments/assets/d8800bbc-10cb-45f7-9f3c-2c7d89908ba8)
### 用 Wireshark 偵測封包
#### 用icmp && ip.dst == 140.122.65.193過濾資料
![image](https://github.com/user-attachments/assets/9a266300-cdf2-4c23-a3a3-7f0fdf1236f9)
| 編號範圍 | 發送端 → 目的端 | 封包類型 | TTL | 備註 |
|----------|----------------|-----------|-----|------|
| 398, 400, 402 | 192.168.102.232 → 140.122.65.193 | ICMP Echo Request | 1 | 無回應（收到 Time Exceeded） |
| 399, 401, 403 | 192.168.103.254 → 192.168.102.232 | ICMP Time Exceeded | 1 | 第一跳路由器回應 |
| 467, 469, 471 | 192.168.102.232 → 140.122.65.193 | ICMP Echo Request | 2 | 無回應（收到 Time Exceeded） |
| 468, 470, 472 | 140.122.127.254 → 192.168.102.232 | ICMP Time Exceeded | 2 | 第二跳路由器回應 |
| 547, 550, 553 | 192.168.102.232 → 140.122.65.193 | ICMP Echo Request | 3 | 無回應（收到 Time Exceeded） |
| 549, 552, 554 | 140.122.6.73 → 192.168.102.232 | ICMP Time Exceeded | 3 | 第三跳路由器回應 |
| 622, 624, 626 | 192.168.102.232 → 140.122.65.193 | ICMP Echo Request | 4 | 無回應（收到 Time Exceeded） |
| 623, 625, 627 | 140.122.6.146 → 192.168.102.232 | ICMP Time Exceeded | 4 | 第四跳路由器回應 |
| 775, 777, 779 | 192.168.102.232 → 140.122.65.193 | ICMP Echo Request | 5 | 有回應（Echo Reply） |
| 776, 778, 780 | 140.122.65.193 → 192.168.102.232 | ICMP Echo Reply | 5 | 成功到達目的地 |

---

## 🛠 封包類型說明

- `ICMP Echo Request`: 用於偵測目標是否回應，也就是「ping」。
- `ICMP Time Exceeded`: 表示封包 TTL 為 0，被中繼路由器丟棄。
- `ICMP Echo Reply`: 表示封包成功到達目的地。

---

## 🌐 路由器節點推測

| TTL | 回應 IP | 節點位置（可能） |
|-----|---------|-----------------|
| 1   | 192.168.103.254 | LAN 閘道（內部） |
| 2   | 140.122.127.254 | 校內路由器（邊界） |
| 3   | 140.122.6.73 | 校內中繼節點 |
| 4   | 140.122.6.146 | 校內或區域網核心 |
| 5   | 140.122.65.193 | 最終目的地 |

---

## 🔍 小結

- 共發送每一跳 3 個封包，觀察穩定性與延遲。
- TTL=5 時成功收到 Echo Reply，代表封包到達目的地。
- 這是典型的 `traceroute` 探測過程。
