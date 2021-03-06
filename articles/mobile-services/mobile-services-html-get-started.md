---
title: HTML/JavaScript アプリ向け Azure Mobile Services の使用 | Microsoft Docs
description: 次のチュートリアルに従って、HTML 開発用の Azure Mobile Services を使用します。
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-html5
ms.devlang: javascript
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: glenga

---
# <a name="getting-started"> </a>Mobile Services の使用
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

## 概要
このチュートリアルでは、Azure Mobile Services を使用して HTML アプリケーションにクラウドベースのバックエンド サービスを追加する方法について説明します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。このチュートリアルの次のビデオ バージョンもご覧ください。

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-the-Mobile-Services-HTML-Client/player]
> 
> 

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、HTML アプリの他のすべての Mobile Services チュートリアルの前提条件です。PhoneGap/Cordova アプリについては、このチュートリアルの [PhoneGap/Cordova バージョン](mobile-services-javascript-backend-phonegap-get-started.md)を参照してください。

## 前提条件
このチュートリアルを完了するには、以下が必要です。

* ローカル コンピューターで次のいずれかの Web サーバーが実行されている必要があります。
  
  * **Windows**: IIS ExpressIIS Express は、[Microsoft Web プラットフォーム インストーラー]によってインストールされます。
  * **MacOS X**: Python。既にインストールされています。
  * **Linux**: Python。[最新バージョンの Python] をインストールする必要があります。
    
    このアプリケーションは任意の Web サーバーを使用してホストすることができますが、これらの Web サーバーは、ダウンロードしたスクリプトでサポートされています。
* HTML5 をサポートする Web ブラウザー。
* Azure アカウント。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank)を参照してください。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい HTML アプリを作成する
モバイル サービスを作成したら、Azure クラシック ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリを変更して、モバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい HTML アプリを作成します。

1. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい HTML アプリを作成する]** を展開します。
   
       ![][6]
   
       これにより、モバイル サービスに接続された HTML アプリを作成し、ホストするための簡単な 3 つの手順が表示されます。
   
      ![][7]
3. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。
4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。
   
      これにより、モバイル サービスに接続する、*To do list* サンプル アプリケーションのプ Web サイト ファイルがダウンロードされます。圧縮されたファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
5. **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力して、**[保存]** をクリックします。
   
      ![][9]
   
   > [!IMPORTANT]
   > localhost 以外の Web サーバーにクイック スタート アプリケーションをデプロイする場合は、**[ホスト名からの要求を許可する]** の一覧に Web サーバーのホスト名を追加する必要があります。詳細については、「[クロス オリジン リソース共有](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx)」を参照してください。
   > 
   > 

## HTML アプリのホストと実行
このチュートリアルの最後に、ローカル コンピューターの新しいアプリケーションをホストして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、**server** サブフォルダーから次のコマンド ファイルの 1 つを開始します。
   
   * **launch-windows** (Windows コンピューター)
   * **launch-mac.command** (Mac OS X コンピューター)
   * **launch-linux.sh** (Linux コンピューター)
     
     > [!NOTE]
     > Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。
     > 
     > 
     
     これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。
2. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。
3. アプリケーションで、意味のあるテキスト (たとえば、*チュートリアルの完了*) を **[Enter new task]** に入力し、**[追加]** をクリックします。
   
       ![][10]
   
       これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目はモバイル サービスによって返され、データはアプリケーションの 2 番目の列に表示されます。
   
   > [!NOTE]
   > モバイル サービスにアクセスしてデータを照会および挿入するコードを確認できます。これは page.js ファイルにあります。
   > 
   > 
4. [Azure クラシック ポータル]に戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。
   
       ![][11]
   
       これで、アプリケーションによってテーブルに挿入されたデータを参照できます。
   
       ![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* **[アプリへの認証の追加]** ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。
* **[Mobile Services HTML/JavaScript の使用方法の概念リファレンス]**HTML/JavaScript で Mobile Services を使用する方法について説明します。

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[アプリへの認証の追加]: mobile-services-html-get-started-users.md

[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Microsoft Web プラットフォーム インストーラー]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[最新バージョンの Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/JavaScript の使用方法の概念リファレンス]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx


<!---HONumber=AcomDC_0727_2016-->