---
title: ロジック アプリで SharePoint Online コネクタを使用する | Microsoft Docs
description: ロジック アプリで Azure App Service SharePoint Online コネクタを使用する方法の概要について説明します。
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe

---
# SharePoint Online コネクタの使用
SharePoint コネクタを使用すると、SharePoint リストを操作できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

ロジック アプリに操作を追加する方法については、[ロジック アプリの作成に関するページ](../app-service-logic/app-service-logic-create-a-logic-app.md)を参照してください。

## トリガーとアクション
SharePoint コネクタは、アクションとして使用できます。SharePoint コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

SharePoint コネクタでは、次のアクションやトリガーを使用できます。

### SharePoint のアクション
実行できるアクションは以下のとおりです。

| アクション | Description |
| --- | --- |
| GetFileMetadata |ドキュメント ライブラリのファイル メタデータを取得するために使用されます |
| UpdateFile |ドキュメント ライブラリのファイルを更新するために使用されます |
| DeleteFile |ドキュメント ライブラリのファイルを削除するために使用されます |
| GetFileMetadataByPath |ドキュメント ライブラリのファイル メタデータを取得するために使用されます |
| GetFileContentByPath |ドキュメント ライブラリのファイルを取得するために使用されます |
| GetFileContent |ドキュメント ライブラリのファイルを取得するために使用されます |
| CreateFile |ドキュメント ライブラリにファイルをアップロードするために使用されます |
| CopyFile |ドキュメント ライブラリのファイルをコピーするために使用されます |
| ExtractFolderV2 |ドキュメント ライブラリのフォルダーを抽出するために使用されます |
| PostItem |SharePoint リストに新しい項目を作成します |
| GetItem |SharePoint リストから 1 つの項目を取得します |
| DeleteItem |SharePoint リストから項目を削除します |
| PatchItem |SharePoint リストの 1 つの項目を更新します |

### SharePoint のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| OnNewFile |SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします |
| OnUpdatedFile |SharePoint フォルダーのファイルが変更されたときにフローをトリガーします |
| GetOnNewItems |SharePoint リストに新しい項目が作成されたとき |
| GetOnUpdatedItems |SharePoint リストの既存の項目が変更されたとき |

## SharePoint への接続を作成する
SharePoint コネクタを使用するには、まず**接続**を作成してから、以下のプロパティの詳細を指定します。

| プロパティ | 必須 | Description |
| --- | --- | --- |
| トークン |はい |SharePoint の資格情報を指定します |

**SharePoint Online** に接続するには、SharePoint Online で ID (ユーザー名とパスワード、スマート カードの資格情報など) を入力する必要があります。認証されたら、ロジック アプリで SharePoint Online コネクタを使用できるようになります。

ロジック アプリのデザイナーで、次の手順に従って SharePoint にサインインし、ロジック アプリで使用する**接続**を作成します。

1. 検索ボックスに「SharePoint」と入力し、名前に SharePoint が含まれるすべてのエントリが返されるまで待ちます。![SharePoint の構成][1]
2. **[SharePoint Online - When a file is created (SharePoint Online - ファイルの作成時)]** を選択します。
3. **[Sign in to SharePoint Online (SharePoint Online にサインイン)]** をクリックします。![SharePoint の構成][2]
4. SharePoint で認証するために、SharePoint 資格情報を入力してサインインします。![SharePoint の構成][3]
5. 認証が完了すると、ロジック アプリにリダイレクトされます。SharePoint の **[When a file is created (ファイルの作成時)]** ダイアログを構成して、ロジック アプリを完成させます。![SharePoint の構成][4]
6. ロジック アプリを完成させるために必要な他のトリガーやアクションを追加できます。
7. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。

## SharePoint REST API リファレンス
#### このドキュメントの対象バージョン: 1.0
### ドキュメント ライブラリのファイル メタデータを取得するために使用されます
**```GET: /datasets/{dataset}/files/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| id |string |○ |path |なし |ファイルの一意識別子 |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイルを更新するために使用されます
**```PUT: /datasets/{dataset}/files/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| id |string |○ |path |なし |ファイルの一意識別子 |
| body | |○ |body |なし |ファイルの内容 |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイルを削除するために使用されます
**```DELETE: /datasets/{dataset}/files/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| id |string |○ |path |なし |ファイルの一意識別子 |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイル メタデータを取得するために使用されます
**```GET: /datasets/{dataset}/GetFileByPath```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| path |string |○ |query |なし |ファイルのパス |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイルを取得するために使用されます
**```GET: /datasets/{dataset}/GetFileContentByPath```**

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| path |string |○ |query |なし |ファイルのパス |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイルを取得するために使用されます
**```GET: /datasets/{dataset}/files/{id}/content```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| id |string |○ |path |なし |ファイルの一意識別子 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリにファイルをアップロードするために使用されます
**```POST: /datasets/{dataset}/files```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |○ |query |なし |フォルダーのパス |
| name |string |○ |query |なし |ファイルの名前 |
| body | |○ |body |なし |ファイルの内容 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのファイルをコピーするために使用されます
**```POST: /datasets/{dataset}/copyFile```**

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| source |string |○ |query |なし |ソース ファイルのパス |
| destination |string |○ |query |なし |宛先ファイルのパス |
| overwrite |boolean |× |query |false |既存のファイルを上書きするかどうか |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします
**```GET: /datasets/{dataset}/triggers/onnewfile```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL |
| folderId |string |○ |query |なし |SharePoint のフォルダーの一意識別子 |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint フォルダーのファイルが変更されたときにフローをトリガーします
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL |
| folderId |string |○ |query |なし |SharePoint のフォルダーの一意識別子 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### ドキュメント ライブラリのフォルダーを抽出するために使用されます
**```POST: /datasets/{dataset}/extractFolderV2```**

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL。例: http://contoso.sharepoint.com/sites/mysite |
| source |string |○ |query |なし |ソース ファイルのパス |
| destination |string |○ |query |なし |宛先フォルダーのパス |
| overwrite |boolean |× |query |false |既存のファイルを上書きするかどうか |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストに新しい項目が作成されたとき
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストの既存の項目が変更されたとき
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**

| Name | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |

### 次のような応答があります。
| Name | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストに新しい項目を作成します
**```POST: /datasets/{dataset}/tables/{table}/items```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| item | |○ |body |なし |作成する項目 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストから 1 つの項目を取得します
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| id |integer |○ |path |なし |取得する項目の一意識別子 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストから項目を削除します
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| id |integer |○ |path |なし |削除する項目の一意識別子 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### SharePoint リストの 1 つの項目を更新します
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |○ |path |なし |SharePoint サイトの URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |○ |path |なし |SharePoint リストの名前 |
| id |integer |○ |path |なし |更新する項目の一意識別子 |
| item | |○ |body |なし |変更されたプロパティがある項目 |

### 次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
## オブジェクト定義:
 **DataSetsMetadata**:

DataSetsMetadata の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| Name | データ型 |
| --- | --- |
| tabular |未定義 |
| BLOB |未定義 |

 **TabularDataSetsMetadata**:

TabularDataSetsMetadata の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

BlobDataSetsMetadata の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

BlobMetadata の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| ID |string |
| Name |string |
| DisplayName |string |
| パス |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

 **Object**:

Object の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
|  | |

 **TableMetadata**:

TableMetadata の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| name |string |
| title |string |
| x-ms-permission |string |
| schema |未定義 |

 **DataSetsList**:

DataSetsList の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| 値 |array |

 **DataSet**:

DataSet の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| Name | データ型 |
| --- | --- |
| 名前 |string |
| DisplayName |string |

 **テーブル**:

Table の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| Name |string |
| DisplayName |string |

 **Item**:

Item の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

ItemsList の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| 名前 | データ型 |
| --- | --- |
| 値 |array |

 **TablesList**:

TablesList の必須プロパティ:

必須のプロパティはありません。

**すべてのプロパティ**:

| Name | データ型 |
| --- | --- |
| 値 |array |

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0824_2016-->