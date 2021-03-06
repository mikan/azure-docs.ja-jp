---
title: Azure Active Directory Identity Protection でリスクを調査する
description: Azure Active Directory Identity Protection で危険なユーザー、検出、およびサインインを調査する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430979"
---
# <a name="how-to-investigate-risk"></a>方法:リスクの調査

Identity Protection を使用すると、お使いの環境のリスクを調査するために使用できる 3 種類のレポートが組織に提供されます。 これらのレポートは、**危険なユーザー**、**危険なサインイン**、および**リスク検出**です。 イベントの調査は、セキュリティ戦略の弱点を理解して識別するための鍵です。

3 つのレポートは、Azure portal の外部で詳細な分析を行うために、どれもイベントを .CSV 形式でダウンロードできます。 危険なユーザー レポートと危険なサインイン レポートでは、最新の 2,500 項目をダウンロードできます。一方、リスク検出レポートでは、最新の 5,000 レコードをダウンロードできます。

組織は、Microsoft Graph API 統合を利用して、データを組織としてアクセスできる他のソースと集計できます。

3 つのレポートは、**Azure portal** >  **[Azure Active Directory]**  >  **[セキュリティ]** にあります。

## <a name="navigating-the-reports"></a>レポート内の移動

各レポートは、レポートの上部に表示されている期間のすべての検出の一覧と共に起動します。 各レポートでは、管理者の設定に基づいて列を追加または削除できます。 管理者は、データを .CSV または .JSON 形式でダウンロードすることを選択できます。 レポートは、レポートの上部にあるフィルターを使用してフィルター処理できます。

個々のエントリを選択して、レポートの上部で追加のエントリを有効にすることができます (サインインが侵害されているか安全であるかを確認する機能、ユーザーが侵害されていることを確認する機能、ユーザー リスクを無視する機能など)。

個々のエントリを選択すると、検出の下に [詳細] ウィンドウが展開されます。 詳細ビューで、管理者は、各検出を調査してアクションを実行できます。 

![危険なサインインと詳細を示している Identity Protection の例](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>危険なユーザー

危険なユーザー レポートによって提供される情報を使用して、管理者は、以下を見つけることができます。

- どのユーザーにリスクがあり、リスクが修復されたか無視されたか
- 検出の詳細
- すべての危険なサインインの履歴
- リスクの履歴
 
管理者は、これらのイベントに対してアクションを実行することを選択できます。 管理者は、以下を実行することを選択できます。

- ユーザー パスワードをリセットする
- ユーザーの侵害を確認する
- ユーザー リスクを無視する
- ユーザーによるサインインをブロックする
- Azure ATP を使用してさらに調査する

## <a name="risky-sign-ins"></a>リスクの高いサインイン

危険なサインイン レポートには、最長で過去 30 日間 (1 か月) のフィルター可能なデータが含まれています。

危険なサインイン レポートによって提供される情報を使用して、管理者は、以下を見つけることができます。

- どのサインインが、危険である、侵害が確認された、安全であると確認された、無視された、または修復されたと分類されているか
- サインイン試行に関連付けられ、リアルタイムで集計されたリスク レベル
- トリガーされた検出の種類
- 適用された条件付きアクセス ポリシー
- MFA の詳細
- デバイス情報
- アプリケーション情報
- 場所情報

管理者は、これらのイベントに対してアクションを実行することを選択できます。 管理者は、以下を実行することを選択できます。

- サインインを侵害ありと確認
- サインインを安全と確認

## <a name="risk-detections"></a>リスク検出

リスク検出レポートには、最長で過去 90 日間 (3 か月) のフィルター可能なデータが含まれています。

リスク検出レポートによって提供される情報を使用して、管理者は、以下を見つけることができます。

- 各リスク検出についての種類を含む情報
- 同時にトリガーされたその他のリスク
- サインインが試行された場所
- Microsoft Cloud App Security (MCAS) のリンクから詳細な情報にアクセスする

管理者は、ユーザーのリスク レポートまたはサインイン レポートに戻り、収集された情報に基づいてアクションを実行できます。

## <a name="next-steps"></a>次のステップ

- [リスクを軽減するために使用できるポリシー](concept-identity-protection-policies.md)

- [サインインとユーザー リスク ポリシーを有効にする](howto-identity-protection-configure-risk-policies.md)
