---
title: Azure Cosmos DB tutorial de distribuição global para API de Tabela
description: Saiba como a distribuição global funciona em Azure Cosmos DB contas de API de Tabela e como configurar a lista de regiões preferida
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: e6cd574d1041908e91ad5e6629403c0e40d11c03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870365"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB com a API de Tabela

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ligar a uma região preferencial com a API de Tabela

Para tirar o máximo partido da [distribuição global](distribute-data-globally.md), as aplicações cliente podem especificar a lista de preferência ordenada de regiões a utilizar nas operações de documentos. Isto pode ser feito através da definição da propriedade [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). O SDK de API de Tabela do Azure Cosmos DB escolhe o melhor ponto final para comunicar com base na configuração da conta, na disponibilidade regional atual e na lista de preferências fornecida.

PreferredLocations deve conter uma lista separada por vírgulas das localizações preferenciais (multi-homing) para leituras. Cada instância do cliente pode especificar um subconjunto dessas regiões pela ordem preferencial para leituras de latência baixa. Os nomes das regiões devem utilizar os respetivos [nomes a apresentar](https://msdn.microsoft.com/library/azure/gg441293.aspx), como, por exemplo, `West US`.

Todas as leituras são enviadas para a primeira região disponível na lista PreferredLocations. Se o pedido falhar, o cliente falhará a lista para a região seguinte e assim sucessivamente.

O SDK tenta ler a partir das regiões especificadas em PreferredLocations. Por exemplo, se a Conta de Base de Dados estiver disponível em três regiões, mas o cliente apenas especificar duas regiões de não escrita para PreferredLocations, não serão fornecidas leituras fora da região de escrita, mesmo em caso de ativação pós-falha.

O SDK envia automaticamente todas as escritas para a região de escrita atual.

Se a propriedade PreferredLocations não estiver definida, todos os pedidos serão fornecidos a partir da região de escrita atual.

Já está, isto conclui este tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente ao ler [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). Para obter mais informações sobre como funciona a replicação de base de dados global no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global do Azure Cosmos DB com APIs de Tabela

