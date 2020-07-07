---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/10/2020
ms.custom: include file
ms.openlocfilehash: bc20707220d3b95c90ff5c39f9a3cde76381322a
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85942348"
---
1. A partir do menu do portal Azure ou da **página Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** procure e selecione **Azure Cosmos DB**.

1. Na página DB do **Azure Cosmos,** selecione **Criar**.

1. Na página **Criar Conta do Azure Cosmos DB**, introduza as definições básicas da nova conta do Azure Cosmos DB. 

    |Definição|Valor|Description |
    |---|---|---|
    |Subscrição|Nome da subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos. |
    |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo** e, em seguida, introduza um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Introduza um nome para identificar a conta do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao nome que indicar para criar o URI, utilize um nome exclusivo.<br><br>O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Deve ter entre 3 a 44 caracteres de comprimento.|
    |API|O tipo de conta a criar|Selecione **Núcleo (SQL)** para criar uma base de dados de documentos e consultar com a sintaxe SQL. <br><br>A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) e MongoDB para dados de documentos, Gremlin para dados gráficos, Tabela Azure e Cassandra. De momento, deve criar uma conta separada para cada API. |
    |Aplicar desconto de nível gratuito|Aplicar ou não aplicar|Com o nível livre Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento gratuitamente numa conta. Saiba mais sobre [o free tier](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos utilizadores para lhes dar o acesso mais rápido aos dados.|
    |Tipo de Conta|Produção ou Não Produção|Selecione **Produção** se a conta for utilizada para uma carga de trabalho de produção. Selecione **Não-Produção** se a conta for utilizada para não produção, por exemplo, desenvolvimento, teste, QA ou encenação. Esta é uma definição de marcação de recursos Azure que afina a experiência portal mas não afeta a conta DB Azure Cosmos subjacente. Pode alterar este valor a qualquer momento.|


    > [!NOTE]
    > Pode ter até uma conta DB Azure Cosmos de nível gratuito por subscrição Azure e deve optar pela criação da conta. Se não vir a opção de aplicar o desconto de nível livre, isto significa que outra conta na subscrição já foi ativada com nível gratuito.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="A página da nova conta do Azure Cosmos DB":::

1. Selecione **Rever + criar**. Pode ignorar as secções **Rede** e **Etiquetas**.

1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="O painel de Notificações de portal do Azure":::

1. Selecione **Ir para recurso** para aceder à página da conta do Azure Cosmos DB. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="A página da conta do Azure Cosmos DB":::
