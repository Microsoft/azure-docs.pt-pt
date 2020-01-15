---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: b1dcf395c55e91a98b237f6e3866c97d74b7a97c
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942638"
---
1. Numa nova janela, inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **criar um recurso**, selecione **bancos de dados**e, em **Azure Cosmos DB**, selecione **criar**.
   
   ![Captura de tela da portal do Azure, realçando mais serviços e Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Na **criar o Azure Cosmos DB conta** página, introduza as definições para a nova conta do Azure Cosmos DB. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|A sua subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos DB. 
    Grupo de Recursos|Criar novo<br><br>Em seguida, introduza o mesmo nome exclusivo, conforme indicado na ID|Selecione **Criar novo**. Em seguida, introduza um nome de grupo de recursos novo para a sua conta. Para manter a simplicidade, utilize o mesmo nome do ID. 
    Nome da Conta|Introduza um nome exclusivo|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB. Como *Mongo.Cosmos.Azure.com* é acrescentado à ID que você fornece para criar seu URI, use uma ID exclusiva.<br><br>O ID pode utilizar apenas letras minúsculas, números e o caráter de hífen (-). Ele deve ter entre 3 e 31 caracteres de comprimento.
    API|API do Azure Cosmos DB para MongoDB|A API determina o tipo de conta a criar. O Azure Cosmos DB fornece cinco APIs: Core (SQL) para bancos de dados de documentos, Gremlin para bancos de dados de grafo, o MongoDB de API Azure Cosmos DB para bancos de dados de documentos, tabela do Azure e Cassandra. Atualmente, tem de criar uma conta separada para cada API. <br><br>Selecione **MongoDB** porque neste guia de início rápido você está criando uma coleção que funciona com o MongoDB.|
    Localização|Selecione a região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.
    Versão|3.6|Selecione o protocolo de transmissão do MongoDB versão 3,6 ou para compatibilidade com versões anteriores, selecione 3,2.

    Selecione **rever + criar**. Pode ignorar o **rede** e **etiquetas** secção. 

    ![A página da nova conta do Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A criação da conta demora alguns minutos. Aguarde até que o portal exiba o **Parabéns! A página sua conta do cosmos com a compatibilidade de protocolo de conexão para MongoDB está pronta** .

    ![O painel de Notificações de portal do Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
