---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77061721"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   ![Criar um recurso no portal Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na **página Nova,** selecione **Databases** > **Azure Cosmos DB**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na página **Create Azure Cosmos DB Account,** introduza as definições para a nova conta Azure Cosmos DB. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, insira o mesmo nome que Nome de Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a sua conta. Para a simplicidade, use o mesmo nome que o nome da conta Azure Cosmos DB. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. A sua conta URI será *mongo.cosmos.azure.com* anexada ao seu nome de conta único.<br><br>O nome da conta só pode usar letras minúsculas, números e hífenes (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Azure Cosmos DB para Mongo DB API|A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) para bases de dados de documentos, Gremlin para bases de dados de gráficos, Azure Cosmos DB para Mongo DB API para bases de dados de documentos, Mesa Azure e Cassandra. De momento, deve criar uma conta separada para cada API. <br><br>Selecione **Azure Cosmos DB para Mongo DB API** porque neste arranque rápido você está criando uma coleção que trabalha com MongoDB.<br><br>[Saiba mais sobre o Azure Cosmos DB para a API MongoDB.](../articles/cosmos-db/mongodb-introduction.md)|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.|

    Selecione **Review+Criar**. Pode saltar a secção **Rede** e **Etiquetas.** 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A criação da conta demora alguns minutos. Aguarde que o portal mostre os **Parabéns! A sua conta Azure Cosmos DB para mongo DB API está pronta.**

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
