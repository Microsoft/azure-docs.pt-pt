---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212623"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Criar um recurso**.
   
   ![Criar um recurso no portal Azure](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na **página Nova,** selecione **Databases** > **Azure Cosmos DB**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na página **Create Azure Cosmos DB Account,** introduza as definições para a nova conta Azure Cosmos DB. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|**Criar novo,** em seguida, Nome de Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a sua conta. Para a simplicidade, use o mesmo nome que o nome da conta Azure Cosmos DB. 
    Nome da Conta|Um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB.<br><br>O nome da conta só pode usar letras minúsculas, números e hífenes (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Tabela|A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) para bases de dados de documentos, Gremlin para bases de dados de gráficos, MongoDB para bases de dados de documentos, Mesa Azure e Cassandra. Deve criar uma conta separada para cada API. <br><br>Selecione **Mesa Azure,** porque neste arranque rápido está a criar uma tabela que funciona com a Tabela API. <br><br>[Saiba mais sobre a Tabela API.](../articles/cosmos-db/table-introduction.md)|
    Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Pode deixar as opções **de Geo-Redundância** e **Multi-região Writes** no **Disable** para evitar encargos adicionais e saltar as secções de **Rede** e **Etiquetas.**

5. Selecione **Review+Criar**. Depois de concluída a validação, selecione **Criar** para criar a conta. 
 
   ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. A criação da conta demora alguns minutos. Verá uma mensagem que diz que **a sua implantação está em andamento.** Aguarde a colocação da colocação e, em seguida, selecione **Ir para o recurso**.

    ![O painel de notificações do portal Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

