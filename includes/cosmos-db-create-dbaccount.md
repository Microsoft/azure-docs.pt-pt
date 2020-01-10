---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 8f8b8384b0c570e2b4925b0e84480b19a632187e
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75751646"
---
1. Vá para a [portal do Azure](https://portal.azure.com/) para criar uma conta de Azure Cosmos DB. Procure e selecione **Azure Cosmos DB**.

   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Selecione **Adicionar**.
1. Na página **criar conta de Azure Cosmos DB** , insira as configurações básicas para a nova conta do Azure Cosmos. 

    |Definição|Valor|Descrição |
    |---|---|---|
    |Subscrição|Nome da subscrição|Selecione a assinatura do Azure que você deseja usar para esta conta do Azure Cosmos. |
    |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **criar novo**e insira um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Insira um nome para identificar sua conta do Azure Cosmos. Como *Documents.Azure.com* é acrescentado ao nome que você fornece para criar seu URI, use um nome exclusivo.<br><br>O nome só pode conter letras minúsculas, números e o caractere de hífen (-). Deve ter entre 3-31 caracteres de comprimento.|
    |API|O tipo de conta a ser criada|Selecione **núcleo (SQL)** para criar um banco de dados de documento e uma consulta usando a sintaxe SQL. <br><br>A API determina o tipo de conta a criar. O Azure Cosmos DB fornece cinco APIs: Core (SQL) e MongoDB para dados de documento, Gremlin para dados de grafo, tabela do Azure e Cassandra. Atualmente, tem de criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API de SQL](../articles/cosmos-db/documentdb-introduction.md).|
    |Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Use o local mais próximo de seus usuários para dar a eles o acesso mais rápido aos dados.|

   ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Selecione **Rever + criar**. Você pode ignorar as seções de **rede** e **marcas** .

1. Examine as configurações da conta e, em seguida, selecione **criar**. Leva alguns minutos para criar a conta. Aguarde até que a página do portal exiba **sua implantação esteja concluída**. 

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecione **ir para o recurso** para acessar a página da conta Azure Cosmos DB. 

    ![A página da conta Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
