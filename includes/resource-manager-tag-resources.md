---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/03/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 6d8f29e9272f3e05bb585c032240cce67c37d2bf
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665139"
---
1. リソースまたはリソース グループのタグを表示するには、概要で既存のタグを探します。 これまでにタグを適用していない場合、リストは空です。
   
     ![リソースまたはリソース グループのタグの表示](./media/resource-manager-tag-resources/view-tags.png)
1. タグを追加するには、 **[タグを追加するにはここをクリックします]** を選択します。

1. 名前と値を指定します。 **+** を選択してタグを追加します。
   
     ![タグの追加](./media/resource-manager-tag-resources/add-tag.png)
1. 必要に応じてタグの追加を続行します。 操作が完了したら、 **[保存]** をクリックします。
   
     ![タグの保存](./media/resource-manager-tag-resources/save-tags.png)
1. タグは、概要に表示されます。
   
     ![タグの表示](./media/resource-manager-tag-resources/view-new-tags.png)
1. タグを追加または削除するには、 **[変更]** を選択します。
   
1. タグを削除するには、ごみ箱アイコンを選択します。 次に、 **[保存]** を選択します。

     ![タグの削除](./media/resource-manager-tag-resources/delete-tag.png)


複数のリソースにタグを一括で割り当てるには:

1. リソースのリストで、タグを割り当てるリソースのチェックボックスをオンにします。

     ![複数のリソースの選択](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. **[タグの割り当て]** を選択します。

     ![タグを割り当てる](./media/resource-manager-tag-resources/assign-tags.png)

1. 各名前と値の後で、 **+** を選択します。 操作が完了したら、 **[割り当て]** を選択します。

     ![割り当ての選択](./media/resource-manager-tag-resources/select-assign.png)

タグが付いているすべてのリソースを表示するには:

1. Azure portal のメニューで **[すべてのサービス]** を選択します。 **[全般]** 、 **[タグ]** の順番に選択します。

     ![タグで検索する](./media/resource-manager-tag-resources/find-tags-general.png)

1. リソースを表示するタグを選択します。

     ![タグの選択](./media/resource-manager-tag-resources/select-tag.png)

1. そのタグの付いたすべてのリソースが表示されます。

     ![タグでリソースを表示する](./media/resource-manager-tag-resources/view-resources-by-tag.png)
