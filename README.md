# Git 風格指南

本文是一篇 Git 的風格指南，受到 [*How to Get Your Change Into the Linux
Kernel*](https://www.kernel.org/doc/Documentation/SubmittingPatches) 以及 [git 操作手冊](http://git-scm.com/doc)和流傳於社群間的最佳實踐所啟發。

本指南有其它語言的翻譯：

* [原文](https://github.com/agis-/git-style-guide)
* [简体中文](https://github.com/aseaday/git-style-guide)
* [葡萄牙文](https://github.com/guylhermetabosa/git-style-guide)

想要貢獻嗎？非常好！複製（Fork）本專案並發送一個收取要求（Pull Request）吧。

# 目錄

* [分支](#分支)
* [提交](#提交)
  - [提交訊息](#提交訊息)
* [合併](#合併)
* [其它](#其它)

## 分支

* 選擇**小巧**具有**描述性的**名稱：

  ```shell
  # 好
  $ git checkout -b oauth-migration

  # 差──過於模糊
  $ git checkout -b login_fix
  ```

* 外部服務對應的問題編號（譬如 GitHub 的 issue）也是分支（Branch）名稱的好選擇。比如：

  ```shell
  # GitHub issue #15
  $ git checkout -b issue-15
  ```

* 使用**連接符號（dash）**來分隔英文單字。

* 幾個人一起開發**同一個**功能的時候，有**自己的**功能分支（Feature branch）以及**團隊共用**的功能分支可能會比較方便。可考慮以下的命名慣例：

  ```shell
  $ git checkout -b feature-a/master # 團隊共用的分支
  $ git checkout -b feature-a/maria  # Maria 的分支
  $ git checkout -b feature-a/nick   # Nick 的分支
  ```

  把個人分支合併到團隊分支裡（參考 [“合併”](#合併)）。
  最後團隊共用的分支會合併回 "master"。

* 合併之後刪除上游程式庫（Repository）的分支（除非有保留的必要）。

  秘訣：在 "master" 分支使用下面的命令來列出已合併的分支：

  ```shell
  $ git branch --merged | grep -v "\*"
  ```

## 提交

* 每次提交應該都是單一的**邏輯變動**。一個提交裡不要做多個**邏輯變動**。譬如若一個修補檔案（Patch），修復錯誤同時最佳化了效能，則將其分為兩次獨立的提交。

* 不要把**單一邏輯變動**分成多個提交。譬如功能的實作以及對應的測試應該屬於同一個提交。

* **盡早提交，時常提交**。小巧、自身完備的提交，在事情出錯時更容易理解與取消（revert）。

* 提交應該按照邏輯的順序排序。假設**乙提交**依賴**甲提交**的修改內容，則**甲提交**應該在**乙提交**之前。

### 提交訊息

* 撰寫提交訊息時，請使用編輯器而不是終端機：

  ```shell
  # 好
  $ git commit

  # 差
  $ git commit -m "Quick fix"
  ```

  從終端裡提交變相鼓勵將所有資訊都塞在一行裡，通常會變成資訊不明確又模糊的提交。

* 總結行（也就是訊息的第一行）應該要簡潔有力。最理想的情況是不超過 **50** 個字元。第一個單字要大寫並用現在命令式時態。不需要有句號因為總結是提交訊息的**標題**：

  ```shell
  # 好──現在命令式、大寫並少於 50 個字元
  Mark huge records as obsolete when clearing hinting faults

  # 差
  fixed ActiveModel::Errors deprecation messages failing when AR was used outside of Rails.
  ```

* 空一行之後接著是更加完整的描述。一行應該要控制在 **72 個字元**以內，並解釋為什麼需要這個變動？解決了什麼問題？以及可能的副作用是什麼。

  也應該要提供任何相關資源的連結（比如連結到 Bug Tracker 對應的問題）：

  ```shell
  簡短總結變動（50 個字元內）

  更加詳細的說明文字（需要的話）。一行保持在 72 個字元。某些情況下，第一行會被視為 Email 的主旨，
  其它行會是 Email 的內容。總結下面空一行非常重要（除非你忽略整個提交的主體）；
  若是總結下面沒有空一行，rebase 就會搞混，而把兩次提交混在一起。

  更多段落先空行再繼續撰寫。

  - 也可以使用項目符號（Bullet points）

  - 項目的符號使用連字號或星號，符號後空一格，之間留空行

  參考：http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
  ```

  最後在撰寫提交訊息時，想想看一年之後回頭看這個提交時，會需要知道什麼內容。

* 若**甲提交**依賴於**乙提交**，則之間的依賴關係應該要在甲提交裡敘述。在引用提交時，請使用提交的 SHA-1。

  同樣若**甲提交**解決一個由**乙提交**引入的 Bug，則應要在甲提交裡說明。

* 若提交之後會整併（squash）到另一個提交裡，則分別使用 `--squash` 以及 `--fixup` 使得意圖更明顯：

  ```shell
  $ git commit --squash f387cab2
  ```

  秘訣：在重校基準（rebase）時使用 `--autosquash`。標註的提交會自動被整併。

## 合併

* **不要重寫已經發佈的版本歷史。**程式碼倉庫的版本歷史本身很有價值，能夠知道究竟發生了什麼事非常重要。修改已經發佈的歷史通常是造成專案參與者問題的根源所在。

* 但有些場合可以重寫歷史是，比如像是：

  * 你是分支唯一的開發者，而且分支不會需要進行程式碼審查（Code Review）。

  * 想要整理分支（譬如：整併無謂的提交）或是為了之後要合併回 "master" 的重校基準（rebase）之用。

  也就是說，**永遠不要重寫 "master" 分支的歷史**，或任何特殊分支的版本歷史（譬如 CI 伺服器所使用的 production 分支）。

* 保持版本歷史簡潔。在合併分支之前：

    - 確保符合風格指南，完成任何需要的前置作業（整併提交、調整提交順序或是修改提交訊息等）。

    - 對即將合併進去的分支做基準校正（rebase）：

      ```shell
      [my-branch] $ git fetch
      [my-branch] $ git rebase origin/master
      # then merge
      ```

      這會使分支可以直接應用在 "master" 分支之上，並會有乾淨的版本歷史。

      **（備註：這個策略適合生命週期短的分支。不然將 "master" 分支合併進來會比重新校正基準好。）**

*   若分支超過一次提交，不要使用 fast-forward 來合併：

    ```shell
    # 好──確保有建立合併提交（merge commit）
    $ git merge --no-ff my-branch

    # 差
    $ git merge my-branch
    ```

## 其它

* 每個工作流程都有各自的優缺點。適合你、你們團隊、你的專案、你的開發流程的工作流程就是好的工作流程。

  也就是，選擇一個工程流程，並堅守這個流程非常重要。

* **保持一致。**這和工作流程有關，但也可以應用到像是提交訊息、分支名稱以及標籤名稱上。程式庫有著一致的風格在檢視記錄或提交的時候更容易了解發生了什麼事。

* **推送前先測試。** 不要把半成品推送上去。

* 使用 [annotated tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Annotated-Tags)，來在版本歷史中標記版本發佈或是重要的時間點。

  自己的話建議採用 [Lightweight Tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Lightweight-Tags)，像是把提交下書籤以供未來參考之用。

* 時常將程式庫保持在良好狀態。在本機及遠端程式庫執行以下維護性任務：

  * [`git-gc(1)`](http://git-scm.com/docs/git-gc)
  * [`git-prune(1)`](http://git-scm.com/docs/git-prune)
  * [`git-fsck(1)`](http://git-scm.com/docs/git-fsck)

# 授權

![cc license](http://i.creativecommons.org/l/by/4.0/88x31.png)

本著作係採用創用 CC 姓名標示 4.0 國際授權條款授權。

# 致謝

Agis Anastasopoulos / [@agisanast](https://twitter.com/agisanast) / http://agis.io
