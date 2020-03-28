---
title: Tutorial de distribuição global azure Cosmos DB para API tabela
description: Saiba como funciona a distribuição global nas contas da API da Tabela Azure Cosmos DB e como configurar a lista preferida das regiões
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900175"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB com a API de Tabela

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ligar a uma região preferencial com a API de Tabela

Para tirar partido da [distribuição global,](distribute-data-globally.md)as aplicações dos clientes devem especificar a localização atual onde a sua aplicação está em execução. Isto é feito `CosmosExecutorConfiguration.CurrentRegion` através da fixação da propriedade. A `CurrentRegion` propriedade deve conter uma única localização. Cada instância de cliente pode especificar a sua própria região para leituras de baixa latência. A região deve ser nomeada utilizando os seus nomes de [exibição,](https://msdn.microsoft.com/library/azure/gg441293.aspx) como "Oeste dos EUA". 

O Azure Cosmos DB Table API SDK escolhe automaticamente o melhor ponto final para comunicar com base na configuração da conta e na disponibilidade regional atual. Prioriza a região mais próxima para proporcionar uma melhor latência aos clientes. Depois de definir `CurrentRegion` a propriedade atual, ler e escrever pedidos são direcionados da seguinte forma:

* **Leia os pedidos:** Todos os pedidos de leitura `CurrentRegion`são enviados para o configurado . Com base na proximidade, o SDK seleciona automaticamente uma região geo-replicada por recuo para alta disponibilidade.

* **Escreva pedidos:** O SDK envia automaticamente todos os pedidos de escrita para a atual região de escrita. Numa conta multi-master, a região atual servirá também os pedidos de escrita. Com base na proximidade, o SDK seleciona automaticamente uma região geo-replicada por recuo para alta disponibilidade.

Se não especificar a `CurrentRegion` propriedade, o SDK utiliza a atual região de escrita para todas as operações.

Por exemplo, se uma conta Azure Cosmos estiver nas regiões "Oeste dos EUA" e "Leste dos EUA". Se "West US" é a região de escrita e o pedido está presente em "East US". Se a propriedade CurrentRegion não estiver configurada, todos os pedidos de leitura e escrita são sempre direcionados para a região "Oeste dos EUA". Se a propriedade CurrentRegion estiver configurada, todos os pedidos de leitura são servidos da região "East US".

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global do Azure Cosmos DB com APIs de Tabela

