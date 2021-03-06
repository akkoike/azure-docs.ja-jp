---
title: "Azure Redis Cache を使用する方法 | Microsoft Docs"
description: "Azure Redis Cache を使用して Azure アプリケーションのパフォーマンスを向上させる方法を説明します"
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 209d4f610f0d5199d9018c506acef3b7328478ef


---
# <a name="how-to-use-azure-redis-cache"></a>Azure Redis Cache の使用方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

このガイドでは、 **Azure Redis Cache**の基本的な使用方法について説明します。 Microsoft Azure Redis Cache は、広く支持されているオープン ソースの Redis Cache がベースとなっています。 マイクロソフトによって管理されている、セキュリティで保護された専用の Redis cache にアクセスすることができます。 Azure Redis Cache を使用して作成されたキャッシュには、Microsoft Azure 内のあらゆるアプリケーションからアクセスすることができます。

Microsoft Azure Redis Cache には、次のレベルがあります。

* **Basic** – 単一ノード。 複数のサイズ、最大 53 GB
* **Standard** – 2 ノード (プライマリ/レプリカ)。 複数のサイズ、最大 53 GB 99.9% の SLA。
* **Premium** – 最大 10 個のシャードがある 2 ノード (プライマリ/レプリカ)。 6 GB から 530 GB までの複数のサイズ (詳細はお問い合わせください)。 Standard レベルのすべての機能と、[Redis クラスター](cache-how-to-premium-clustering.md)、[Redis の永続化](cache-how-to-premium-persistence.md)、[Azure Virtual Network](cache-how-to-premium-vnet.md) のサポートを含むその他の機能。 99.9% の SLA。

各レベルは、機能と価格ごとに異なります。 価格の詳細については、[Cache の価格詳細][Cache の価格詳細]に関するページをご覧ください。

このガイドでは、C\# を使用する [StackExchange.Redis][StackExchange.Redis] クライアントの使用方法について説明します。 紹介するシナリオは、**キャッシュの作成と構成**、**キャッシュ クライアントの構成**、**キャッシュでのオブジェクトの追加と削除**などです。 Azure Redis Cache の使用方法の詳細については、「[次のステップ][次のステップ]」を参照してください。 Redis Cache で ASP.NET MVC Web アプリを作成する詳しい手順を示したチュートリアルについては、「 [Redis Cache で Web アプリを作成する方法](cache-web-app-howto.md)」を参照してください。

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Azure Redis Cache の使用
Azure Redis Cache の導入は簡単です。 使い始めるには、キャッシュをプロビジョニングして構成します。 次に、キャッシュ クライアントを構成してキャッシュにアクセスできるようにします。 キャッシュ クライアントを構成すると、使い始めることができます。

* [キャッシュの作成][キャッシュの作成]
* [キャッシュ クライアントの構成][キャッシュ クライアントの構成]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>キャッシュの作成
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>作成したキャッシュにアクセスするには
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

キャッシュの構成の詳細については、「 [Azure Redis Cache の構成方法](cache-configure.md)」を参照してください。

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>キャッシュ クライアントの構成
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

クライアント プロジェクトをキャッシュ用に構成できたら、以降のセクションで説明されている、キャッシュを操作するための技法を使用できます。

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>キャッシュの操作
このセクションの手順では、キャッシュに対する一般的なタスクを行う方法について説明します。

* [キャッシュに接続する][キャッシュに接続する]
* [オブジェクトをキャッシュに追加する、キャッシュから削除する][オブジェクトをキャッシュに追加する、キャッシュから削除する]
* [キャッシュ内で .NET オブジェクトを使用する](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>キャッシュに接続する
プログラムでキャッシュを操作するには、キャッシュへの参照が必要です。 StackExchange.Redis クライアントを使用して Azure Redis Cache にアクセスするすべてのファイルの先頭に次のコードを追加します。

    using StackExchange.Redis;

> [!NOTE]
> StackExchange.Redis クライアントには、.NET Framework 4 以降が必要です。
> 
> 

Azure Redis Cache への接続には、 `ConnectionMultiplexer` クラスを使用します。 このクラスはクライアント アプリケーションの開始から終了まで共有、再利用する前提で設計されており、操作単位で作成する必要はありません。 

Azure Redis Cache に接続して、接続済みの `ConnectionMultiplexer` インスタンスを取得するには、次の例のように、静的 `Connect` メソッドを呼び出して、キャッシュのエンドポイントとキーを渡します。 password パラメーターには、Azure ポータルから生成されたキーを使用してください。

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> 警告: ソース コード内に資格情報を保存することは絶対に避けてください。 このサンプルでは、単純化するためにあえてソース コード内に記述しています。 資格情報を保存する方法については、[アプリケーション文字列と接続文字列の動作][アプリケーション文字列と接続文字列の動作]に関するページをご覧ください。
> 
> 

SSL を使用しない場合は、`ssl=false` を設定するか、`ssl` パラメーターを省略します。

> [!NOTE]
> 既定では、新しいキャッシュに対して非 SSL ポートは無効になっています。 非 SSL ポートを有効にする手順については、「 [アクセス ポート](cache-configure.md#access-ports)」を参照してください。
> 
> 

アプリケーション内の `ConnectionMultiplexer` インスタンスを共有する方法の 1 つは、次の例のように、接続されたインスタンスを返す静的プロパティの設定です。 これにより、接続された `ConnectionMultiplexer` インスタンス 1 つのみがスレッド セーフな方法で初期化されます。 これらの例では、 `abortConnect` が false に設定されており、Azure Redis Cache への接続が確立されていない場合でも呼び出しが成功します。 `ConnectionMultiplexer` の主な機能の 1 つは、ネットワーク問題などの原因が解決されると、キャッシュへの接続が自動的に復元されることです。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

高度な接続構成オプションの詳細については、[StackExchange.Redis の構成モデル][StackExchange.Redis の構成モデル]を参照してください。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

接続が確立されたら、 `ConnectionMultiplexer.GetDatabase` メソッドを呼び出して Redis Cache データベースへの参照を取得します。 `GetDatabase` メソッドから返されるオブジェクトは、手付かずで受け渡しされる軽量のオブジェクトであり、保存する必要はありません。

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

以上、Azure Redis Cache インスタンスに接続して、キャッシュ データベースへの参照を取得する方法を説明しました。今度は実際にキャッシュを使用してみましょう。

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>オブジェクトをキャッシュに追加する、キャッシュから削除する
キャッシュに項目を格納する、またはキャッシュから項目を取得するには、`StringSet` メソッドと `StringGet` メソッドを使用します。

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis では、ほとんどのデータが Redis 文字列として保存されますが、これらの文字列には、さまざまなデータ型を格納することができます。シリアル化したバイナリ データもその 1 つで、.NET のオブジェクトをキャッシュに保存する際に使用することができます。

`StringGet` を呼び出すと、オブジェクトが存在する場合はそのオブジェクトが返され、存在しない場合は `null` が返されます。 その場合、目的のデータ ソースから値を取得してキャッシュに格納しておき、後で使用することができます。 これを "キャッシュ アサイド パターン" といいます。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

キャッシュ内の項目の有効期限を指定するには、`StringSet` の `TimeSpan` パラメーターを使用します。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>キャッシュ内で .NET オブジェクトを使用する
Azure Redis Cache はプリミティブ データ型に加え、.NET オブジェクトをキャッシュできますが、.NET オブジェクトをキャッシュするためには、あらかじめシリアル化しておく必要があります。 この処理はアプリケーション開発者が行わなければなりません。逆にそのことでシリアライザーの選択に幅が生まれ、開発者にとってのメリットとなっています。

オブジェクトをシリアル化する簡単な方法の 1 つは、[Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) の `JsonConvert` シリアル化方法を使用して、JSON へおよび JSON からシリアル化する方法です。 次の例では、 `Employee` オブジェクト インスタンスを使用した get および set を示しています。

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>次のステップ
これで、基本を学習できました。Azure Redis Cache の詳細については、次のリンク先を参照してください。

* Azure Redis Cache の ASP.NET プロバイダーを参照してください。
  * [Azure Redis セッション状態プロバイダー](cache-aspnet-session-state-provider.md)
  * [Azure Redis Cache ASP.NET 出力キャッシュ プロバイダー](cache-aspnet-output-cache-provider.md)
* [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。 Azure ポータルでメトリックを表示できますが、お好みのツールを使用して、メトリックを [ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) することも可能です。
* [StackExchange.Redis キャッシュ クライアントのドキュメント][StackExchange.Redis キャッシュ クライアントのドキュメント]を参照してください。
  * Azure Redis Cache は、さまざまな Redis クライアントや開発言語からアクセスできます。 詳細については、[http://redis.io/clients][http://redis.io/clients] を参照してください。
* Azure Redis Cache は、Redsmin や Redis Desktop Manager などのサードパーティのサービスやツールと共に使用することもできます。
  * Redsmin の詳細については、[Azure Redis 接続文字列を取得し、Redsmin と共に使用する方法][Azure Redis 接続文字列を取得し、Redsmin と共に使用する方法]に関するページをご覧ください。
  * [Redis Desktop Manager](https://github.com/uglide/RedisDesktopManager)を使用して GUI で Azure Redis Cache 内のデータにアクセスし、データを確認します。
* [redis][redis] のドキュメント、[redis のデータ型][redis のデータ型]に関するページ、および [redis のデータ型の概念][redis のデータ型の概念]に関するページをご覧ください。

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Azure Redis Cache の概要 (ビデオ)]: #video
[Azure Redis Cache とは]: #what-is
[Azure Cache の作成]: #create-cache
[最適なキャッシュの種類]: #choosing-cache
[Azure Cache を使用するための Visual Studio プロジェクトの準備]: #prepare-vs
[Caching を使用するようにアプリケーションを構成する]: #configure-app
[Azure Redis Cache の使用]: #getting-started-cache-service
[キャッシュの作成]: #create-cache
[キャッシュの構成]: #enable-caching
[キャッシュ クライアントの構成]: #NuGet
[キャッシュの操作]: #working-with-caches
[キャッシュに接続する]: #connect-to-cache
[オブジェクトをキャッシュに追加する、キャッシュから削除する]: #add-object
[キャッシュ内のオブジェクトの有効期限を指定する]: #specify-expiration
[ASP.NET セッション状態をキャッシュに格納する]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[他の言語での Azure Redis Cache の開発]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Azure Redis 接続文字列を取得し、Redsmin と共に使用する方法]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis セッション状態プロバイダー]: http://go.microsoft.com/fwlink/?LinkId=398249
[方法: プログラムによってキャッシュ クライアントを構成する]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Azure Managed Cache Service のセッション状態プロバイダー]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric キャッシュ: セッション状態のキャッシュ]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure のキャッシュの出力キャッシュ プロバイダーに関するページ]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[チーム ブログ]: http://blogs.msdn.com/b/windowsazure/
[Azure Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[仮想マシンのサイズの構成方法]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Cache の容量計画に関する考慮事項]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[方法: 宣言によって ASP.NET ページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[方法: プログラムによってページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure Redis Cache でのキャッシュの構成]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis の構成モデル]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[キャッシュ内で .NET オブジェクトを使用する]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet パッケージ マネージャーのインストールのページ]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache の価格詳細]: http://www.windowsazure.com/pricing/details/cache/
[Azure App Service で Java Web アプリ]: https://portal.azure.com/

[Azure Redis Cache の概要]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Azure Redis Cache への移行]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache のサンプル]: http://go.microsoft.com/fwlink/?LinkId=320840
[リソース グループを使用した Azure リソースの管理]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis キャッシュ クライアントのドキュメント]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis データ型]: http://redis.io/topics/data-types
[redis のデータ型の概念に関するページ]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work (アプリケーション文字列と接続文字列の動作) (アプリケーション文字列と接続文字列の動作)]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=Nov16_HO2-->


