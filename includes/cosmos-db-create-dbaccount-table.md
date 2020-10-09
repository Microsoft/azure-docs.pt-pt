---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5015371afc67574a214097f8d3eef661df29c22f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85115048"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, **selecione Criar um recurso.**
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Criar um recurso no portal do Azure":::
   
3. Na página **Nova,** selecione **Databases**  >  **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Criar um recurso no portal do Azure":::
   
3. Na página **De Conta DB Create Azure Cosmos,** insira as definições para a nova conta DB da Azure Cosmos. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|**Criar novo,** em seguida, nome de conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a sua conta. Para simplificar, use o mesmo nome que o nome da sua conta DB Azure Cosmos. 
    Nome da Conta|Um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB.<br><br>O nome da conta só pode usar letras minúsculas, números e hífens (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Tabela|A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) para bases de dados de documentos, Gremlin para bases de dados de gráficos, MongoDB para bases de dados de documentos, Tabela Azure e Cassandra. Deve criar uma conta separada para cada API. <br><br>Selecione **Azure Table**, porque neste arranque rápido está a criar uma tabela que funciona com a Tabela API. <br><br>[Saiba mais sobre a Tabela API.](../articles/cosmos-db/table-introduction.md)|
    Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Pode deixar as opções de Escrita de **Geo-Redundância** e **Multi-região** no **Disable** para evitar custos adicionais e saltar as secções **de Rede** e **Tags.**

5. Selecione **Review+Create**. Depois de concluída a validação, selecione **Criar** para criar a conta. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="Criar um recurso no portal do Azure":::

6. A criação da conta demora alguns minutos. Verá uma mensagem que diz que **a sua implantação está em curso.** Aguarde que a implementação termine e, em seguida, selecione **Ir para o recurso**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Criar um recurso no portal do Azure":::

