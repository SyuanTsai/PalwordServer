
### 免責聲明

**我只是提供指令，我會用我所知道的知識說明會有甚麼安全疑慮，如果有任何安全疑慮的請物使用。**


### 環境

原則上使用中華電信與Windwos系統的都能夠使用
1. 網路環境是使用中華電信並使用固定IP
2. Windwos 10 個人專業版。

## 流程

1. 安裝
這邊可以用Windwos/Linux/Docker Desktop ... 等，
有很多種方式，就不另外說明請參考他人文章，我主要說明網路環境問題。


2. 確認你的網路環境

    基本網路環境應該是這兩種情況，
    如果你是租屋或者網路是社區大樓共享的那種請直接購買VPN，
    因為這些地方不會因為你的需求開Port。

    A.  小烏龜直接連線
    ```mermaid
    graph LR
        A[中華電信小烏龜] --> B(電腦主機)
    ```

    B.  小烏龜中間還有一個Rounter連線
    ```mermaid
    graph LR
        A[中華電信小烏龜] --> B(Router)
        B --> C(電腦主機)

    ```

3. 確認你的連線方式
    這邊有幾個名詞
    硬體撥接，意思是從數據機/Router輸入你的ISP業者的帳號密碼連線。
    ISP連線，意思是輸入網路業者(ISP)所提供的帳號密碼連線。


    A. 主機直接ISP連線 => [網路環境A](####網路環境A)
    ```mermaid
    graph LR
        A[中華電信小烏龜] --> B(電腦主機自己連線)
    ```
    B. 小烏龜串接到Router，Router在串接到電腦，電腦使用ISP連線
    ```mermaid
    graph LR
        A[中華電信小烏龜] --> B(Router)
        B --> C(電腦主機自己連線)
    ```

    C. 小烏龜使用硬體撥接 => [Port forwarding](###4.開通Port_forwarding)
    ```mermaid
    graph LR
        A[中華電信小烏龜] -->|透過小烏龜硬體撥接| B(電腦主機)
    ```


    D. 小烏龜串接到Router，在Router使用硬體撥接，在分享到電腦 => [Port forwarding](###4.開通Port_forwarding)
    ```mermaid
    graph LR
        A[中華電信小烏龜] --> B(Router)
        B -->|透過Router使用ISP連線| C(電腦主機)
    ```     


    E. 小烏龜使用硬體撥接後再分享到Router接著在分享到電腦 => [Port forwarding](###4.開通Port_forwarding)
    這個比較特殊，我自己沒玩過，但應該小烏龜跟你的Router都要開Port forwarding
    ```mermaid
    graph LR
        A[中華電信小烏龜] -->|透過小烏龜使用ISP連線| B(Router)
        B --> C(電腦主機)
    ```

4. 開通Port_forwarding

    請注意要Port forwarding，有些會出現Port Triggering
    這部分請參考其他文章 [伺服器網路設定詳細教學](https://forum.gamer.com.tw/C.php?bsn=71458&snA=68&tnum=9&bPage=5)


5. 開通防火牆

    防火牆可以被看作是一棟社區大樓的門衛，
    負責檢查每一位試圖進入大樓的訪客。僅當訪客符合事先設定的標準（即特定的Port）時，
    防火牆才會允許其進入。這樣的安排確保了只有獲得許可的Port可以進入，從而維護了系統安全。

    而關閉防火牆，便好比將這棟社區大樓的門衛撤走，讓大門敞開。
    這意味著任何人都可以自由地來到你家的門前或窗下，嘗試進入。
    在這種情況下，缺乏必要的監控和限制，大大增加了安全風險，使得家庭暴露於潛在的不安全因素之中。

    內網好比是一棟社區大樓內的住戶，可以自由地互相拜訪，直到深夜仍然敲門交流。
    外網則像是位於大樓外的訪客，若想聯繫大樓內的某位住戶，必須通過社區大樓的守衛進行。

    #### 網路環境A
    請開啟你的PowerSheel，並給予系統控制者的權限。
    下面的命令會幫你建立一個名稱叫做"Palword Port 8211 UDP"的開通規則，同時允許外網訪問。
    ```shell
    netsh advfirewall firewall add rule name="Palword Port 8211 UDP" dir=in action=allow protocol=UDP localport=8211 profile=any
    ```


    #### 網路環境B,C,D,E

    請開啟你的PowerSheel，並給予系統控制者的權限。
    下面的命令會幫你建立一個名稱叫做"Palword Port 8211 UDP"的開通規則，同時限制只允許內網訪問。
    ```shell
    netsh advfirewall firewall add rule name="Palword Port 8211 UDP" dir=in action=allow protocol=UDP localport=8211 profile=private
    ```




## 其他檢測手段

1. 開啟你的主機回應Ping的功能

    Ping回應是甚麼，大概就是你的手機有沒有自動答覆功能一樣。
    可以確定你的電話打得通，就這樣而已。


    如果下面指令沒用請Google 怎麼開啟Ping，這邊只需要開IPV4的就好。

    開啟
    ```shell
    netsh firewall set icmpsetting 8 enable
    ```

    關閉
    ```shell
    netsh firewall set icmpsetting 8 disable
    ```

2. 外部測試
    請從下面網只測試能否Ping到自己的IP，IP怎麼查不再贅述

    查IP的網頁
    https://www.whatismyip.com.tw/tw/
    ,
    https://www.myip.com/

    Ping的網頁
    https://ping.eu/ping/
3. 如果Ping的到，但還是無法連線，那只會有防火牆或Port forwarding的問題，恭喜你解決就可以當Host了。
4. 如果Ping不到，有很多可能性，網路前面還有一層，或者萬惡的中華小烏龜。


###

