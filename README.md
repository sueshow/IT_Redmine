# PM_Redmine

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
* 功能：權限管理、自訂標籤、議題指派及監看、議題相關程式碼追蹤、markdown語法筆記
* 其他
  * [plan.io](https://plan.io/)公司 fork redmine 的程式碼實作成自己的版本，並為 redmine 增加更多功能。該公司提供按月付費使用和免費版
    * 免費版限制：1 project, 2 users, 10 customers, 500mb storage
    * 註冊網址：https://accounts.plan.io/signup/Bronze?locale=en
<br>


## 建立 Redmine
* 使用的工具是 Docker (Google 的業餘開源專案)，主要是因為輕量、更易於擴充及分享的特性而選用
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
    * root帳密：root | password
  * phpMyAdmin：
    * 設定 MySQL 連線帳密及網路
    * 設定對外的 port 為 8080
  * redmine
    * 設定 MySQL 連線帳密及網路
    * 設定平台管理員帳密：admin | password
    * 設定對外的 port 為 80
  * 尚須設定的資料：平台寄信信箱 (預設使用 Gmail 的信箱寄信)
    * 注意如有使用到已有二階段認證 (如手機二次確認登入) 的信箱，請申請一個應用程式密碼替代為密碼登入，不然可能沒辦法授權給平台使用寄信
* 建立 Redmine 平台
  * 開啟終端機，切換到與設定檔 (docker-compose.yml) 同個目錄下
  * 執行
    > docker-compsoe up -d
    * up：啟動
    * -d：在背景執行
  * 執行成功後會看到個別的服務指定 done 表示有啟動成功，但不代表已經各個服務間有完全串連成功
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


## Redmine 和 Outlook 巨集
* 目的：透過 Outlook 的巨集呼叫 Redmine API，新增 Redmine Issue
* 紀錄重要資訊
  * assigned_to_id 
    * 登入後，把滑鼠游標移至右上角「登入者帳號」，畫面的左下角會有一個 URL
    * 在網址：伺服器的網址/users/數字，數字即為assigned_to_id
  * API 存取金鑰
    * 點選「我的帳戶」
    * 點選「API 存取金鑰」下的顯示，其即為 API 存取金鑰
  * project_id
    * 點選「我的帳戶」
    * 按「F12」或「點右鍵」→ 檢查「Elements」
    * 按「Ctrl+F」搜尋「project_id」，即可找到「1 ~ 多個符合的專案 id」
* 匯入憑證
  * 進入「控制台」 → 「網路和網際網路」 → 「網際網路選項」
  * 點選頁籤「內容」 → 「憑證(C)」 → 頁籤「個人」 → 「匯入(I)...」
  * 進行「匯入」
    * 點選「下一步」
    * 點選「瀏覽」 → 選擇憑證檔(.crt)，並點選「下一步」
    * 不須輸入密碼，點選「下一步」
    * 確認憑證存放區是選擇「個人」，並點選「下一步」
    * 點選「完成」，可以看到匯入執行成功的訊息
    * 確認頁籤「個人」下，會新增一個憑證檔
* Outlook 目錄路徑
  * 在信件所在資料夾，點右鍵 → 選「內容」
* 編輯 VBA：vbaScript.txt
  ```
  Option Explicit

  Dim cusItemColl As Collection '存放多筆告警project的 Outlook.Folder.Items
  Dim outlookApp As Outlook.Application
  Dim olNs As Outlook.NameSpace
  Dim redmineKey As String
  Dim assignToId As Integer

  Private Sub Application_Startup()
      'On Error GoTo ErrorHandler
    
      Debug.Print ("startup")
    
      Dim colStores As Outlook.Stores
      Dim oStore As Outlook.Store
    
      redmineKey = "放入API金鑰"           '請填入API金鑰
      assignToId = 放入assignd_to_id       '請填入assignd_to_id
   
      Set cusItemColl = New Collection
      Set outlookApp = Outlook.Application
      Set olNs = Application.GetNamespace("MAPI")
    
      '有幾種告警信，就加上幾個對應的內容。createWarningProjectData 是 function name，每呼叫一次代表你想監測的一種告警
      createWarningProjectData "放入你的系統簡稱", 放入project_id, "放入告警信的寄件者信箱", "放入告警信主旨的關鍵字串", "放入告警信放置路徑"
    
      Debug.Print ("  ")
            
  'ExitNewItem:
  'Exit Sub
     
  'ErrorHandler:
  '    Debug.Print (Err.Number & " - " & Err.Description)
  '    Resume ExitNewItem
  End Sub

  Sub createWarningProjectData(systemName As String, projectId As Integer, senderEmail As   String, subjectKeyword As String, folderPath As String)
      Dim specificFolder As Outlook.Folder
      Dim cusItemsObj As New CusItems
    
      Set cusItemsObj = New CusItems
        
      Set cusItemsObj.App = Outlook.Application
      Set specificFolder = GetFolderItemsfromPath(folderPath)
      Debug.Print ("specificFolder.FolderPath" & specificFolder.folderPath)
      Set cusItemsObj.items = specificFolder.items
    
      cusItemsObj.redmineKey = redmineKey
      cusItemsObj.assignToId = assignToId
      cusItemsObj.systemName = systemName
      cusItemsObj.projectId = projectId
      cusItemsObj.comparedSenderEmail = senderEmail
      cusItemsObj.comparedSubject = subjectKeyword
    
      cusItemColl.Add cusItemsObj 'Collection加入這個新的CusItems
  End Sub

  Function GetFolderItemsfromPath(path As String) As Outlook.Folder
      Dim myRootFolder As Outlook.Folder
      Dim subFolder As Outlook.Folder
      Dim newPath As String
      Dim folderStr() As String
      Dim J As Integer
    
      'path e.g. \\Sueshow'MailBox\收件匣\error
      newPath = Replace(path, "\\", "")
      'Debug.Print ("newPath: " & newPath)
      folderStr = Split(newPath, "\")

      For J = LBound(folderStr) To UBound(folderStr)
          If J = 0 Then
              Set myRootFolder = olNs.Folders(folderStr(J))
              'Debug.Print ("myRootFolder.folderPath" & myRootFolder.folderPath)
          Else
              Set subFolder = myRootFolder.Folders(folderStr(J))
              Set myRootFolder = subFolder '為了取得下一個folder
              'Debug.Print ("subFolder.folderPath" & subFolder.folderPath)
          End If
      Next J
    
      'Debug.Print ("(Final)subfolder.folderPath" & subFolder.folderPath)

      Set GetFolderItemsfromPath = subFolder
  End Function
  ```
* Outlook 巨集設定
  * 進入 Outlook 點選「檔案」
  * 點選「選項」 → 開啟 Outlook 選項視窗後，點選「信任中心」
  * 「信任中心設定(I)」 → 開啟後，點選「巨集設定」 → 勾選「經過數位簽章的巨集會顯示通知，其他所有巨集會停用」或 「所有巨集都顯示通知」 → 點選「確定」
* Outlook 使用快取模式
  * 設定好快取模式後，建議不要再修改，如欲修改，需暫時調整取消快取模式，並清空 VBA
  * 步驟
    * 進入 Outlook 點選「檔案」
    * 點選「資訊」 → 「帳戶設定」 → 「帳戶設定(A)」
    * Double Click 自己的 exchange mail 
    * 跳出伺服器設定，並勾選「使用快取 Exchange 模式」 → 點選「下一步」
    * 出現訊息告知需重啟 Outlook，並點選「確定」 → 「完成」 → 「關閉」
    * 重新啟動 Outlook 後，需稍等待 Outlook 把 Exchange Server 上的 mail download 下來
* Outlook 加上巨集(VBA)，並做數位簽名
  * 在 Outlook 按 ALT+F11 會出現程式編輯視窗，請 dobule click 視窗左側的「ThisOutlookSession」，視窗右側會出現對應的內容
  * 【註】若「ThisOutlookSession」不是空白，表示原本已設定巨集，先將原內容備份到文字檔後，再把視窗內的內容清空，避免巨集發生衝突
  * 把 vbaScript.txt 修改好的內容 copy 到「ThisOutlookSession」中，並點選「儲存」
  * 匯入 CusItems.cls 檔
    * 對「ThisOutlookSession」上方的專案「點右鍵」 → 點選「匯入檔案(I)...」 → 選擇「Cusltems.cls」 → 點選「開啟」
      ```
      # Cusltems.cls 的內容
      VERSION 1.0 CLASS
      BEGIN
        MultiUse = -1  'True
      END
      Attribute VB_Name = "CusItems"
      Attribute VB_GlobalNameSpace = False
      Attribute VB_Creatable = False
      Attribute VB_PredeclaredId = False
      Attribute VB_Exposed = False
      Public WithEvents App As Outlook.Application
      Attribute App.VB_VarHelpID = -1
      Public WithEvents items As Outlook.items
      Attribute items.VB_VarHelpID = -1


      Public redmineKey As String
      Public assignToId As Integer
      Public systemName As String
      Public projectId As Integer, i As Integer
      Public comparedSenderEmail As String
      Public comparedSubject As String


      Private Sub Class_Initialize()
          '注意！這個空method不能移掉。這樣才會觸發事件
      End Sub

      Private Sub Items_ItemAdd(ByVal item As Object)
          On Error GoTo ErrorHandler

          Debug.Print ("***Items_ItemAdd start")
    
          Dim aErr
          Dim http As Object
          
          Dim nowStr As String  '今天
          Dim exEmail As String  '從信件中抓取 mail的寄件者
          Dim dueDateStr As String  '完成日期
          Dim addDays As Integer
    
          addDays = 3  '從今天開始加幾天為「完成日期」
    
          Debug.Print ("now: " & Format(Now, "YYYY-MM-DD hh:mm:ss"))

 
          If (TypeOf item Is MailItem) Then
              If item.SenderEmailType = "EX" Then  'Microsof Exchange
                  exEmail = item.Sender.GetExchangeUser.PrimarySmtpAddress
              Else  'SMTP
                  exEmail = item.SenderEmailAddress
              End If
        
      
              Debug.Print ("mail subject: " & item.subject)
              Debug.Print ("exEmail: " & exEmail)
        
        
              nowStr = Format(Now, "YYYY-MM-DD")
              dueDateStr = Format(DateAdd("d", addDays, Now), "YYYY-MM-DD")
        
        
              Debug.Print ("systemName: " & systemName)
              Debug.Print ("projectId: " & projectId)
              Debug.Print ("comparedSenderEmail: " & comparedSenderEmail)
              Debug.Print ("comparedSubject: " & comparedSubject)
              Debug.Print ("InStr(Item.subject, comparedSubject) > 0: " & (InStr(item.subject, comparedSubject) > 0))
              Debug.Print ("LCase(comparedSenderEmail) = LCase(exEmail): " & (LCase(comparedSenderEmail) = LCase(exEmail)))
    
    
              If (InStr(item.subject, comparedSubject) > 0 And LCase(comparedSenderEmail) = LCase(exEmail)) Then '有subject字串 and comparedSenderEmail和寄件者相同
                  Debug.Print ("Matched System is : " & systemName & ", matched mail subject ==> " & item.subject)
        
                  Dim receiveMailTimeStr As String
                  Dim subjectStr As String
                  Dim httpResult As String
                  Dim jsonBodyStr As String
                  Dim origMailBody As String
                  Dim bodyLength As Integer
        
                  receiveMailTimeStr = Format(item.ReceivedTime, "YYYY-MM-DD hh:mm:ss")  '收到信件的日期
                  subjectStr = "(收信時間: " & receiveMailTimeStr & ")" & Replace(item.subject, """", "")  '去掉"，要不然會造成json出錯
                  origMailBody = Replace(Replace(Replace(Left(item.Body, 2000), """", ""), Chr(10), "\n"), Chr(13), "")  '取Body的前2000個字，並去掉", 換行符號[Chr(10), Chr(13)]，要不然會造成json出錯
            
                  Dim RegEx As Object
                  Set RegEx = CreateObject("VBScript.RegExp")
                  On Error Resume Next


                  ' use RegEx 做 replace，是因為發現可能有些特殊字元，會造成send json給Redmine時，回400 Bad Request
                  RegEx.Global = True
                  RegEx.Pattern = "\s\\n"
                  origMailBody = RegEx.Replace(origMailBody, "\n")
                  RegEx.Pattern = "\\n\s"
                  origMailBody = RegEx.Replace(origMailBody, "\n")
                  RegEx.Pattern = "\\n\\n"
                  origMailBody = RegEx.Replace(origMailBody, "\n")
                  RegEx.Pattern = "\\r\\n"
                  origMailBody = RegEx.Replace(origMailBody, "\n")
            
            
                  jsonBodyStr = "{ ""issue"": { ""priority_id"": 1, ""status_id"": 1, ""tracker_id"": 4, ""project_id"": " & projectId & ", ""assigned_to_id"": " & assignToId & ", ""start_date"": """ & nowStr & """, ""subject"": """ & subjectStr & """, ""description"": """ & Trim(origMailBody) & """, ""due_date"": """ & dueDateStr & """ } }"
                  Debug.Print ("jsonBodyStr: " & jsonBodyStr)
         
            
                  Set http = CreateObject("Microsoft.XMLHTTP")
                  On Error Resume Next
                  http.Open "POST", "http://redmine.tstartel.com/issues.json", False
                  aErr = Array(Err.Number, Err.Description)
                  On Error GoTo 0
            
                  If 0 = aErr(0) Then
                      http.setRequestHeader "CONTENT-TYPE", "application/json"
                      http.setRequestHeader "X-Redmine-API-Key", redmineKey
                      On Error Resume Next
                      http.Send jsonBodyStr
                      aErr = Array(Err.Number, Err.Description)
                      On Error GoTo 0
                
                      Select Case True
                          Case 0 <> aErr(0)
                              Debug.Print ("Send failed: " & aErr(0) & ", " & aErr(1))
                          Case 201 = http.Status 'success
                              httpResult = http.responseText
                              Debug.Print ("httpResult: " & httpResult)
                          Case Else
                              Debug.Print ("http.status: " & http.Status & " http.statusText: " & http.statusText)
                      End Select
            
                  Else
                      Debug.Print ("Open failed: " & aErr(0) & ", " & aErr(1))
                  End If
            
                  Set http = Nothing
            
              End If
        
              Debug.Print ("  ")
              
          End If
    

          Debug.Print ("=====================================================")
    
    
      ExitNewItem:
      Exit Sub
    
    
      ErrorHandler:
          Debug.Print (Err.Number & " - " & Err.Description)
          Resume ExitNewItem

      End Sub
      ```
    * 匯入成功後可看見物件類別模組中出現 CusItems，點選「儲存」
  * 為巨集加上數位簽名
    * 點選「工具(I)」 → 「數位簽名(D)...」 
    * 點選「選擇(C)...」 → 可以看見方才匯入的憑證，點「確定」 → 點「確定」 → 點選「儲存」 → 關閉並回到 Microsoft Outlook
  * 關閉 Outlook，會詢問是否要儲存 VBA 專案，點選「是」
  * 再次開啟 Outlook 時，請點選「顯示簽章詳細資料」 → 確認憑證後關閉 → 「信任來自於這個發行者的所有文件」
<br>


## 參考資料
* [半小時以 Docker 建立 Redmine 平台](https://nick-chen.medium.com/%E5%8D%8A%E5%B0%8F%E6%99%82%E4%BB%A5-docker-%E8%87%AA%E5%BB%BA-redmine-%E5%B9%B3%E5%8F%B0-e2f3e683fea5)
* [plan.io 的 Redmine 使用說明](https://afunction.gitbooks.io/tools/content/pms/redmine.html)
* [CentOS 結合 Docker + Redmine](https://ithelp.ithome.com.tw/articles/10192972)
<br>
