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
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305362"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No painel de navegação esquerdo, selecione **criar um recurso**. Selecione **bases de dados** e, em seguida, selecione **do Azure Cosmos DB**.
   
   ![Captura de ecrã do portal do Azure, destaque para mais serviços e do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Sobre o **criar o Azure Cosmos DB conta** página, introduza as definições para a nova conta do Azure Cosmos DB:
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, introduza o mesmo nome exclusivo, conforme indicado na ID|Selecione **Criar novo**. Em seguida, introduza um nome de grupo de recursos novo para a sua conta. Para manter a simplicidade, utilize o mesmo nome do ID. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB.<br><br>O ID pode utilizar apenas letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 a 31 carateres de comprimento.
    API|Tabela do Azure|A API determina o tipo de conta a criar. O Azure Cosmos DB oferece cinco APIs: Core(SQL) bases de dados do documento, Gremlin para bases de dados do gráfico, o MongoDB para bases de dados de documentos, tabelas do Azure e Cassandra. Atualmente, tem de criar uma conta separada para cada API. <br><br>Selecione **tabelas do Azure** porque neste início rápido está a criar uma tabela que funciona com a API de tabela. <br><br>[Saiba mais sobre a API de tabela](../articles/cosmos-db/table-introduction.md).|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

    Pode deixar a **Georredundância** e **escreve de várias regiões** opções com os valores padrão (**desativar**) para evitar cobranças adicionais do RU. Pode ignorar o **rede** e **etiquetas** secções.

5. Selecione **rever + criar**. Depois de concluída a validação, selecione **criar** para criar a conta. 
 
   ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Demora alguns minutos para criar a conta. Verá uma mensagem que indica **sua implementação está em curso**. Aguarde até a implementação concluir e, em seguida, selecione **Ir para recurso**.

    ![O painel de notificações do portal do Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
