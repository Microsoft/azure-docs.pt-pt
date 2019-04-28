---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/24/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 3915df46495e754843009a761e9a07aa2daddae2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686804"
---
1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**.
   
   ![O painel da base de dados do portal do Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na **criar o Azure Cosmos DB conta** página, introduza as definições para a nova conta do Azure Cosmos DB. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|*A sua subscrição*|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>*Introduzir um nome exclusivo*|Selecione **Criar Novo** e, em seguida, introduza um novo nome de grupo de recursos para a sua conta. Para simplificar, pode utilizar o mesmo nome como nome da sua conta. 
    Nome da Conta|*Introduzir um nome exclusivo*|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. Uma vez que *documents.azure.com* é anexado ao ID que indicar para criar o seu URI, utilize um ID exclusivo.<br><br>O ID só pode conter letras minúsculas, números e o caráter de hífen (-) e tem de ter entre 3 a 31 carateres de comprimento....
    API|Núcleo (SQL)|A API determina o tipo de conta a criar. O Azure Cosmos DB oferece cinco APIs: SQL (base de dados do documento), Gremlin (base de dados do gráfico), MongoDB (base de dados do documento), API de tabela e API de Cassandra. Atualmente, cada uma das APIs exige que crie uma conta separada. <br><br>Selecione **Core (SQL)** porque este artigo irá criar uma base de dados de documentos e consultas com sintaxe SQL. <br><br>[Saiba mais sobre a API SQL](../articles/cosmos-db/documentdb-introduction.md)|
    Location|*Selecione a região mais próxima dos seus utilizadores*|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.
    Ativar redundância geográfica| Deixar em branco | Esta ação cria uma versão replicada da base de dados numa segunda região (emparelhada). Deixe em branco.  
    Escritas de várias regiões| Deixar em branco | Isto permite que cada uma das suas regiões de base de dados para ser de leitura e a região de escrita. Deixe em branco.  

    Em seguida, clique em **Rever + criar**. Pode ignorar o **rede** e **etiquetas** secção. 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-preview.png)

    Reveja as informações de resumo e clique em **criar**. 

    ![A página de resumo de verificação de conta](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-summary-preview.png)

4. A criação da conta demora alguns minutos. Aguarde que o portal apresentar os **a implementação estiver concluída** da mensagem e clique em **Ir para recurso**.     

    ![A página da conta criada com êxito](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-complete-preview.png)

5. O portal apresenta o **Parabéns! A sua conta do Azure Cosmos DB foi criada**.

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

