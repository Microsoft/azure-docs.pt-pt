---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a6cfafd41d1632d99b7ca03bce2316ec679d7579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061865"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   ![Criar um recurso no portal Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na **página Nova,** selecione **Databases** > **Azure Cosmos DB**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na página **Create Azure Cosmos DB Account,** introduza as definições para a nova conta Azure Cosmos DB. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, insira o mesmo nome que Nome de Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a sua conta. Para a simplicidade, use o mesmo nome que o nome da conta Azure Cosmos DB. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. A sua conta URI será *gremlin.azure.com* anexada ao seu nome de conta único.<br><br>O nome da conta só pode usar letras minúsculas, números e hífenes (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Gremlin (gráfico)|A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) para bases de dados de documentos, Gremlin para bases de dados de gráficos, MongoDB para bases de dados de documentos, Mesa Azure e Cassandra. Deve criar uma conta separada para cada API. <br><br>Selecione **Gremlin (gráfico)** porque neste arranque rápido está a criar uma tabela que funciona com a API Gremlin. <br><br>[Saiba mais sobre a API Gremlin.](../articles/cosmos-db/graph-introduction.md)|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Selecione **Review+Criar**. Pode saltar a secção **Rede** e **Etiquetas.** 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. A criação da conta demora alguns minutos. Aguarde que o portal mostre os **Parabéns! A sua conta Azure Cosmos DB foi criada** página.
   
   ![Página criada pela conta Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

