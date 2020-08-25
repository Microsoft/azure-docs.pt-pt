---
title: Tutorial de distribuição global Azure Cosmos DB para API de Mesa
description: Saiba como funciona a distribuição global nas contas API da Tabela DB da Azure Cosmos e como configurar a lista preferida das regiões
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "76900175"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB com a API de Tabela

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ligar a uma região preferencial com a API de Tabela

Para tirar partido da [distribuição global,](distribute-data-globally.md)as aplicações do cliente devem especificar a localização atual onde a sua aplicação está em execução. Isto é feito através da definição da `CosmosExecutorConfiguration.CurrentRegion` propriedade. A `CurrentRegion` propriedade deve conter uma única localização. Cada instância de cliente pode especificar a sua própria região para leituras de baixa latência. A região deve ser nomeada utilizando os seus [nomes de exibição,](https://msdn.microsoft.com/library/azure/gg441293.aspx) como "West US". 

A Azure Cosmos DB Table API SDK escolhe automaticamente o melhor ponto final para comunicar com base na configuração da conta e na disponibilidade regional atual. Prioriza a região mais próxima para proporcionar uma melhor latência aos clientes. Depois de definir a propriedade atual, os `CurrentRegion` pedidos de leitura e escrita são dirigidos da seguinte forma:

* **Leia os pedidos:** Todos os pedidos de leitura são enviados para os configurados `CurrentRegion` . Com base na proximidade, o SDK seleciona automaticamente uma região geo-replicada de recuo para uma elevada disponibilidade.

* **Escrever pedidos:** O SDK envia automaticamente todos os pedidos de escrita para a atual região de escrita. Numa conta multi-master, a região atual também servirá os pedidos de escrita. Com base na proximidade, o SDK seleciona automaticamente uma região geo-replicada de recuo para uma elevada disponibilidade.

Se não especificar a `CurrentRegion` propriedade, o SDK utiliza a região de escrita atual para todas as operações.

Por exemplo, se uma conta Azure Cosmos estiver nas regiões "West US" e "East US". Se "West US" é a região de escrita e a aplicação está presente em "East US". Se a propriedade CurrentRegion não estiver configurada, todos os pedidos de leitura e escrita são sempre direcionados para a região "West US". Se a propriedade Da Região Atual estiver configurada, todos os pedidos de leitura são servidos da região "East US".

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global do Azure Cosmos DB com APIs de Tabela

