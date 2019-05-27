---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 127d67cc3b5dcd0ddd585470821eb1baa08c2388
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151379"
---
Pode agora utilizar o Data Explorer para adicionar dados à sua coleção nova.

1. No Data Explorer, a base de dados nova aparece no painel Coleções. Expanda a base de dados **Tarefas**, expanda a coleção **Itens**, clique em **Documentos** e clique em **Documentos Novos**. 

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Agora, adicione um documento à coleção com a seguinte estrutura.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Depois de ter adicionado o json ao separador **Documentos**, clique em **Guardar**.

    ![Copie os dados json e clique em Guardar no Data Explorer no portal do Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Crie e guarde mais um documento onde insere um valor exclusivo para a propriedade `id` e altere as outras propriedades conforme necessário. Agora, os documentos podem ter qualquer estrutura que queira criar, uma vez que o Azure Cosmos DB não impõe qualquer esquema aos seus dados.