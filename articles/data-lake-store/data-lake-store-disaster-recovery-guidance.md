---
title: Azure Data Lake Storage Gen1 のディザスター リカバリーのガイダンス | Microsoft Docs
description: Azure Data Lake Storage Gen1 のディザスター リカバリーのガイダンス
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966065"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 内のデータに対するディザスター リカバリーのガイダンス

Azure Data Lake Storage Gen1 によって、ローカル冗長ストレージ (LRS) が与えられます。 そのため、Data Lake Storage Gen1 アカウント上のデータは、データセンター内で発生する一時的なハードウェア障害に対して、自動レプリカによる回復性を備えています。 これにより、持続性と高可用性を確保し、Data Lake Storage Gen1 の SLA を達成しています。 この記事では、まれに発生するリージョン全体の障害や誤削除からデータをさらに保護する方法に関するガイダンスを示します。

## <a name="disaster-recovery-guidance"></a>ディザスター リカバリーの ガイダンス
すべての顧客が独自のディザスター リカバリー計画を準備することが重要です。 ディザスター リカバリー計画を作成するには、この記事の情報をお読みください。 独自の計画を作成する際に役立つリソースがあります。

* [Azure アプリケーションのディザスター リカバリーと高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure の回復性技術ガイダンス](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>ベスト プラクティス
ディザスター リカバリー計画のニーズに合った頻度で、別のリージョンにある別の Data Lake Storage Gen1 アカウントに重要なデータをコピーすることをお勧めします。 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md)、[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) など、データをコピーするさまざまな方法があります。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

地域的な障害が発生した場合は、データのコピー先のリージョン内のデータにアクセスできます。 [Azure Service Health ダッシュボード](https://azure.microsoft.com/status/)を監視して、世界中の Azure サービスの状態を確認できます。

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>データの破損または誤削除からの復旧に関するガイダンス
Data Lake Storage Gen1 は自動レプリカによるデータの回復性を備えていますが、アプリケーション (または開発者やユーザー) によるデータの破損や誤った削除を防ぐことはできません。

### <a name="best-practices"></a>ベスト プラクティス
誤削除を防ぐために、まず、Data Lake Storage Gen1 アカウントの適切なアクセス ポリシーを設定することをお勧めします。  これには、重要なリソースをロックダウンするための [Azure リソースのロック](../azure-resource-manager/management/lock-resources.md)の適用と、利用可能な [Data Lake Storage Gen1 セキュリティ機能](data-lake-store-security-overview.md)を使用したアカウントおよびファイル レベルのアクセス制御の適用が含まれます。 また、別の Data Lake Storage Gen1 アカウント、フォルダー、または Azure サブスクリプションで、[ADLCopy](data-lake-store-copy-data-azure-storage-blob.md)、[Azure PowerShell](data-lake-store-get-started-powershell.md)、または [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) を使用して、重要なデータのコピーを定期的に作成することもお勧めします。  これを使用して、データの破損や削除から復旧できます。 Azure Data Factory は、データ移動パイプラインを作成して定期的にデプロイできる便利なサービスです。

また、組織で Data Lake Storage Gen1 アカウントの[診断ログ](data-lake-store-diagnostic-logs.md)を有効にすることもできます。これにより、ファイルを削除または更新した可能性のあるユーザーに関する情報を提供する、データ アクセスの監査証跡を収集できます。

## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)

