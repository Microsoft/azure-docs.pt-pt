---
title: Edit a knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker allows you to manage the content of your knowledge base by providing an easy-to-use editing experience.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326790"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Edit a knowledge base in QnA Maker

QnA Maker allows you to manage the content of your knowledge base by providing an easy-to-use editing experience.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Edit your knowledge base content

1.  Select **My knowledge bases** in the top navigation bar. 

    You can see all the services you created or shared with you sorted in the descending order of the **last modified** date.

    ![My Knowledge Bases](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Select a particular knowledge base to make edits to it.
 
1. Select **Settings**. Here you can edit mandatory field Service Name.
  
    |Objetivo|Ação|
    |--|--|
    |Add URL|You can add new URLs to add new FAQ content to Knowledge base by clicking **Manage knowledge base -> '+ Add URL'** link.|
    |Delete URL|You can delete existing URLs by selecting the delete icon, the trash can.|
    |Refresh content|If you want your knowledge base to crawl the latest content of existing URLs, select the **Refresh** checkbox. This will update the knowledge base with latest URL content once. This is not setting a regular schedule of updates.|
    |Add file|You can add a supported file document to be part of a knowledge base, by selecting **Manage knowledge base**, then selecting **+ Add File**|
    |Importar|You can also import any existing knowledge base by selecting **Ímport Knowledge base** button. |
    |Atualizar|Updating of knowledge base depends on **management pricing tier** used while creating QnA Maker service associated with your knowledge base. You can also update the management tier from Azure portal if required.

1. Once you are done making changes to the knowledge base, select **Save and train** in the top right corner of the page in order to persist the changes.    

    ![Save and Train](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >If you leave the page before selecting **Save and train**, all changes will be lost.

## <a name="add-a-qna-pair"></a>Adicione um par de FAQ

On the **EDIT** page, select **Add QnA pair** to add a new row to the knowledge base table.

![Add QnA pair](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Delete a QnA pair

To delete a QnA, click the **delete** icon on the far right of the QnA row. This is a permanent operation. It can't be undone. Consider exporting your KB from the **Publish** page before deleting pairs. 

![Delete QnA pair](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Add alternate questions

Add alternate questions to an existing QnA pair to improve the likelihood of a match to a user query.

![Add Alternate Questions](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Add metadata

Add metadata pairs by first selecting **View options**, then selecting **Show metadata**. This displays the metadata column. Next, select the **+** sign to add a metadata pair. This pair consists of one key and one value.

![Add Metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Make sure to periodically Save and train the knowledge base after making edits to avoid losing changes.

## <a name="manage-large-knowledge-bases"></a>Manage large knowledge bases

* **Data source groups**: The QnAs are grouped by the data source from which they were extracted. You can expand or collapse the data source.

    ![Use the QnA Maker data source bar to collapse and expand data source questions and answers](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Search knowledge base**: You can search the knowledge base by typing in the text box at the top of the Knowledge Base table. Click enter to search on the question, answer, or metadata content. Click on the X icon to remove the search filter.

    ![Use the QnA Maker search box above the questions and answers to reduce the view to only filter-matching items](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Pagination**: Quickly move through data sources to manage large knowledge bases

    ![Use the QnA Maker pagination features above the questions and answers to move through pages of questions and answers](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Delete knowledge bases

Deleting a knowledge base (KB) is a permanent operation. It can't be undone. Before deleting a knowledge base, you should export the knowledge base from the **Settings** page of the QnA Maker portal. 

If you share your KB with [collaborators](collaborate-knowledge-base.md) then delete it, everyone loses access to the KB. 

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure 

If you delete any of the Azure resources used for your QnA Maker knowledge bases, the knowledge bases will no longer function. Before deleting any resources, make sure you export your knowledge bases from the **Settings** page. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Collaborate on a knowledge base](./collaborate-knowledge-base.md)
