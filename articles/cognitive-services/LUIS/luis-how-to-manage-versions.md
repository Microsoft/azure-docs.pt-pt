---
title: Manage versions - LUIS
titleSuffix: Azure Cognitive Services
description: Versions allow you to build and publish different models. A good practice is to clone the current active model to a different version of the app before making changes to the model.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221893"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Use versions to edit and test without impacting staging or production apps

Versions allow you to build and publish different models. A good practice is to clone the current active model to a different [version](luis-concept-version.md) of the app before making changes to the model. 

To work with versions, open your app by selecting its name on **My Apps** page, and then select **Manage** in the top bar, then select **Versions** in the left navigation. 

The list of versions shows which versions are published, where they are published, and which version is currently active. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page](./media/luis-how-to-manage-versions/versions-import.png "Manage section, versions page")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clone a version

1. Select the version you want to clone then select **Clone** from the toolbar. 

2. In the **Clone version** dialog box, type a name for the new version such as "0.2".

   ![Clone Version dialog box](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Version ID can consist only of characters, digits or '.' and cannot be longer than 10 characters.
 
   A new version with the specified name is created and set as the active version.

## <a name="set-active-version"></a>Set active version

Select a version from the list, then select **Activate** from the toolbar. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page, make a version action](./media/luis-how-to-manage-versions/versions-other.png "Manage section, versions page, make a version action")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import version

You can import a `.json` or a `.lu` version of your application.

1. Select **Import** from the toolbar, then select the format. 

2. In the **Import new version** pop-up window, enter the new ten character version name. You only need to set a version ID if the version in the file already exists in the app.

    ![Manage section, versions page, importing new version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Once you import a version, the new version becomes the active version.

### <a name="import-errors"></a>Import errors

* Tokenizer errors: If you get a **tokenizer error** when importing, you are trying to import a version that uses a different [tokenizer](luis-language-support.md#custom-tokenizer-versions) than the app currently uses. To fix this, see [Migrating between tokenizer versions](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Other actions

* To **delete** a version, select a version from the list, then select **Delete** from the toolbar. Selecione **Ok**. 
* To **rename** a version, select a version from the list, then select **Rename** from the toolbar. Enter new name and select **Done**. 
* To **export** a version, select a version from the list, then select **Export app** from the toolbar. Choose JSON to export for backup, choose **Export for container** to [use this app in a LUIS container](luis-container-howto.md).  

