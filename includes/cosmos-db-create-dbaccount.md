---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 93652273dda16e93bdcf9123d7d4a9b3fc956fe9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466178"
---
1. Vá para a [portal do Azure](https://portal.azure.com/) para criar uma conta de Azure Cosmos DB. Procure e selecione **Azure Cosmos DB**.

   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Selecione **Adicionar**.
1. Na página **criar conta de Azure Cosmos DB** , insira as configurações básicas para a nova conta do Azure Cosmos. 

    |Definição|Valor|Descrição |
    |---|---|---|
    |Subscrição|Nome da assinatura|Selecione a assinatura do Azure que você deseja usar para esta conta do Azure Cosmos. |
    |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **criar novo**e insira um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Insira um nome para identificar sua conta do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo.<br><br>A ID só pode conter letras minúsculas, números e o caractere de hífen (-). Deve ter entre 3-31 caracteres de comprimento.|
    |API|O tipo de conta a ser criada|Selecione **núcleo (SQL)** para criar um banco de dados de documento e uma consulta usando a sintaxe SQL. <br><br>A API determina o tipo de conta a criar. O Azure Cosmos DB fornece cinco APIs: Core (SQL) e MongoDB para dados de documento, Gremlin para dados de grafo, tabela do Azure e Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API do SQL](../articles/cosmos-db/documentdb-introduction.md).|
    |Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Use o local mais próximo de seus usuários para dar a eles o acesso mais rápido aos dados.|

   ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Selecione **Rever + criar**. Você pode ignorar as seções de **rede** e **marcas** .

1. Examine as configurações da conta e, em seguida, selecione **criar**. Leva alguns minutos para criar a conta. Aguarde até que a página do portal exiba **sua implantação esteja concluída**. 

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecione **ir para o recurso** para acessar a página da conta Azure Cosmos DB. 

    ![A página da conta Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
