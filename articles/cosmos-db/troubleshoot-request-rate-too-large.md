---
title: Resolução de problemas Azure Cosmos DB taxa de pedido de exceções demasiado grandes
description: Saiba como diagnosticar e corrigir taxas de pedido demasiado grandes.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411342"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnosticar e resolver problemas A taxa de pedido de DB da Azure Cosmos é demasiado grande.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Uma mensagem "Taxa de pedido demasiado grande" ou código de erro 429 indica que os seus pedidos estão a ser estrangulados.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A secção seguinte contém causas e soluções conhecidas para demasiados pedidos.

### <a name="check-the-metrics"></a>Verifique as métricas
Verifique [a monitorização DB da Azure Cosmos](monitor-cosmos-db.md) para ver o número de 429 exceções.

#### <a name="cause"></a>Causa:
A produção consumida (Unidades de Pedido por segundo) excedeu a [produção prevista](set-throughput.md). O SDK recauchuta automaticamente os pedidos com base na política de retíria especificada. Se você obtém este fracasso com frequência, considere aumentar a produção na coleção. Verifique as métricas do portal para ver se está a ter 429 erros. Reveja a sua chave de partição para garantir que resulta numa [distribuição uniforme do armazenamento e do volume de pedidos.](partitioning-overview.md)

#### <a name="solution"></a>Solução:
1. Utilize o [portal ou o SDK](set-throughput.md) para aumentar a produção a provisionada.
1. Mude a base de dados ou o recipiente para [Autoscale.](provision-throughput-autoscale.md)

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).