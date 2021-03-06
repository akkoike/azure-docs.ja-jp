---
title: Azure Active Directory B2B コラボレーションの現在のプレビューの制限事項 | Microsoft Docs
description: Azure Active Directory B2B は、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで、会社間のリレーションシップをサポートします
services: active-directory
documentationcenter: ''
author: viv-liu
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali

---
# Azure AD B2B コラボレーション プレビュー: 現在のプレビューの制限事項
* Multi-Factor Authentication (MFA) が外部ユーザーに対してサポートされていません。たとえば、Contoso では MFA が有効であり、パートナー組織では無効である場合、B2B コラボレーションを通してパートナー組織のユーザーに MFA を許可することはできません。
* 招待は CSV によってのみ可能です。個別の招待および API アクセスはサポートされていません。
* Azure AD の全体管理者のみが .csv ファイルをアップロードできます。
* 一般消費者向け電子メール アドレス (hotmail.com、Gmail.com、comcast.net など) への招待状は現在サポートされていません。
* オンプレミスのアプリケーションへの外部ユーザー アクセスはテストされていません。
* 実際のユーザーがディレクトリから削除された場合、外部ユーザーは自動的にはクリーンアップされません。
* 配布リストへの招待はサポートされていません。
* CSV を使用してアップロードできるレコードは最大 2,000 件です。

## 関連記事
Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [動作のしくみ](active-directory-b2b-how-it-works.md)
* [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
* [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
* [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
* [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0706_2016-->