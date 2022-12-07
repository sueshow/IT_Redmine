# IT_Redmine

## 介紹 Redmine
* 優缺點
  * 優點
    * Redmine 是免費的，若會安裝也會維護的話，可以省下不少錢
    * 彈性的角色管理
    * 有甘特圖與日曆功能可使用
    * 開 issue 與開 ticket 很方便
    * 支援透過電子郵件新增問題
    * 有繁體中文介面可使用
  * 缺點
    * 要自己安裝，而且電腦要裝好 Ruby on Rails 的環境
* 功能
  * 免費版限制：1 project, 2 users, 10 customers, 500mb storage
  * 功能：權限管理、自訂標籤、議題指派及監看、議題相關程式碼追蹤、markdown語法筆記
<br>


## 建立 Redmine
* 使用的工具是 Docker(Google 的業餘開源專案)，主要是因為輕量、更易於擴充及分享的特性而選用
* 安裝 Docker 及 Docker-compose 
  * 安裝連結：https://docs.docker.com/compose/install/
  * 安裝後開啟終端機，確認能取得版本，表示安裝成功
    > docker -v 
    > docker-compose -v
* 撰寫 docker-compose.yml
  * 開啟編輯器，將以下設定檔貼上並儲存名為 docker-compose.yml
  * 這裡選用 bitnami 提供的 redmine image
    ```
    ```
* 設定 docker-compose 內容
  * MySQL：
    * root帳密：root | password 或 admin | admin_password
  * phpMyAdmin：
    * 設定 MySQL 連線帳密及網路
    * 設定對外的 port 為 8080
  * redmine
    * 設定 MySQL 連線帳密及網路
    * 設定平台管理員帳密：admin | password
    * 設定對外的 port 為 80
  * 尚須設定的資料：平台寄信信箱(預設使用 Gmail 的信箱寄信)
    * 注意如有使用到已有二階段認證(如手機二次確認登入)的信箱，請申請一個應用程式密碼替代為密碼登入，不然可能沒辦法授權給平台使用寄信
* 建立 Redmine 平台
  * 開啟終端機，切換到與設定檔 (docker-compose.yml) 同個目錄下，然後執行
    > docker-compsoe up -d
  * 執行成功後會看到個別的服務指定 done 表示有啟動成功，但不代表已經各個服務間有完全串連成功哦
* 確認 Redmine 服務串連建立成功
  * 開啟瀏覽器輸入 http://127.0.0.1:8080 觀看 phpMyAdmin 是否已經啟動
  * 啟動後一段時間還看不到畫面的可以使用指令查看服務狀況
    > docker logs redmine
<br>


## 使用 Redmine 平台
* 登入 Redmine 平台
  * 設定 Redmine 中文介面：Administration => Setting => Display => 預設語言(Default language)設定：繁體中文
  * 設定 Redmine 寄信的項目及主要的服務網址
    * 中文字儲存及寄信測試：網站管理 => 用戶清單=> 建立新用戶
      * 建立一個包含中文字訊息的新用戶內容
      * 勾選「寄送帳戶資訊電子郵件給用戶」
* 重啟服務：docker-compose restart
* 關閉服務：docker-compose stop
<br>


## 參考資料
* [半小時以 Docker 建立 Redmine 平台](https://nick-chen.medium.com/%E5%8D%8A%E5%B0%8F%E6%99%82%E4%BB%A5-docker-%E8%87%AA%E5%BB%BA-redmine-%E5%B9%B3%E5%8F%B0-e2f3e683fea5)
* [Redmine](https://afunction.gitbooks.io/tools/content/pms/redmine.html)
<br>
