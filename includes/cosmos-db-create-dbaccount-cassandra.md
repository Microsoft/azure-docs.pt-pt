---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 10/01/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 1d7af1b29d1ed2d8ff0ae60da0a171b9d8b98bbf
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743160"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, **selecione Criar um recurso.**
   
   ![Criar um recurso no portal do Azure](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na página **Nova,** selecione **Databases**  >  **Azure Cosmos DB**.
   
   ![O painel Bases de dados do portal do Azure](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na página **De Conta DB Create Azure Cosmos,** insira as definições para a nova conta DB da Azure Cosmos. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, insira o mesmo nome que Nome de Conta|Selecione **Criar novo**. Em seguida, insira um novo nome de grupo de recursos para a sua conta. Para simplificar, use o mesmo nome que o nome da sua conta Azure Cosmos. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. A sua conta URI será *cassandra.cosmos.azure.com* anexada ao seu nome de conta único.<br><br>O nome da conta só pode usar letras minúsculas, números e hífens (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Cassandra|A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) para bases de dados de documentos, Gremlin para bases de dados de gráficos, MongoDB para bases de dados de documentos, Tabela Azure e Cassandra. Deve criar uma conta separada para cada API. <br><br>Selecione **Cassandra**, porque neste arranque rápido você está criando uma mesa que funciona com a API Cassandra. <br><br>[Saiba mais sobre a API para Cassandra](../articles/cosmos-db/cassandra-introduction.md).|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Selecione **Review+Create**. Pode saltar a secção **'Networking',** **Backup,** **Encryption** and **Tags.** 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. A criação da conta demora alguns minutos. Aguarde que o portal apresente a página a dizer **parabéns! A sua conta DB Azure Cosmos foi criada.**

