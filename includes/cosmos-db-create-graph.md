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
ms.openlocfilehash: a24159a482c55496bb1e6031be951196258a6796
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565504"
---
Agora, pode utilizar a ferramenta Data Explorer no portal do Azure para criar uma base de dados de gráfico. 

1. Clique em **Data Explorer** > **Novo Gráfico**.

    A área **Adicionar Gráfico** é apresentada na extremidade direita, pelo que poderá ter de se deslocar para a direita para vê-la.

    ![O Data Explorer do portal do Azure, página Adicionar Gráfico](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. Na página **Adicionar gráfico**, introduza as definições para o novo gráfico.

    Definição|Valor sugerido|Descrição
    ---|---|---
    ID da base de dados|base de dados de exemplo|Designe a nova base de dados como *sample-database*. Os nomes das bases de dados têm de ter entre um e 255 carateres e não podem conter `/ \ # ?` nem espaços à direita.
    Débito|400 RUs|Altere o débito para 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.
    ID do Graph|gráfico de exemplo|Denomine a nova coleção como *sample-graph*. Os nomes dos gráficos têm os mesmos requisitos de carateres que os IDs das bases de dados.
    Chave de partição| /pk |Todas as contas de Cosmos DB precisam de uma chave de partição para dimensionar horizontalmente. Saiba como selecionar uma chave de partição apropriada no [artigo particionamento de dados de grafo](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

3. Assim que o formulário estiver preenchido, clique em **OK**.
