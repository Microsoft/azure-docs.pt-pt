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
ms.openlocfilehash: 975bb5ee194e7bd9538660878cbed20c943fcf52
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184808"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Clique em **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**.
   
   ![Painel "Base de Dados" do portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Na **criar o Azure Cosmos DB conta** página, introduza as definições para a nova conta do Azure Cosmos DB. 

    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, introduza o mesmo nome exclusivo, conforme indicado na ID|Selecione **Criar novo**. Em seguida, introduza um nome de grupo de recursos novo para a sua conta. Para manter a simplicidade, utilize o mesmo nome do ID. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo.<br><br>O ID pode utilizar apenas letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 a 31 carateres de comprimento.
    API|Gremlin (gráfico)|A API determina o tipo de conta a criar. O Azure Cosmos DB oferece cinco APIs: Core(SQL) bases de dados do documento, Gremlin para bases de dados do gráfico, o MongoDB para bases de dados de documentos, tabelas do Azure e Cassandra. Atualmente, tem de criar uma conta separada para cada API. <br><br>Selecione **Gremlin (gráfico)** porque neste início rápido está a criar uma tabela que funciona com a API do Gremlin. <br><br>[Saiba mais sobre a Graph API](../articles/cosmos-db/graph-introduction.md).|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Selecione **rever + criar**. Pode ignorar o **rede** e **etiquetas** secção. 

    ![O painel da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. A criação da conta demora alguns minutos. Aguarde que o portal apresente a página **Parabéns! A sua conta do Azure Cosmos DB foi criada**.

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)