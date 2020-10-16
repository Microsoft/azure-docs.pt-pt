---
title: Resolução de problemas Azure Cosmos DB taxa de pedido de exceções demasiado grandes
description: Saiba como diagnosticar e corrigir taxas de pedido demasiado grandes.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871093"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnosticar e resolver problemas A taxa de pedido de DB da Azure Cosmos é demasiado grande.
Uma mensagem "Taxa de pedido demasiado grande" ou código de erro 429 indica que os seus pedidos estão a ser estrangulados.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A secção seguinte contém causas e soluções conhecidas para demasiados pedidos.

### <a name="check-the-metrics"></a>Verifique as métricas
Verifique [a monitorização DB da Azure Cosmos](monitor-cosmos-db.md) para ver o número de 429 exceções.

#### <a name="cause"></a>Causa:
A produção consumida (Unidades de Pedido por segundo) excedeu a [produção prevista](set-throughput.md). O SDK recauchuta automaticamente os pedidos com base na política de retíria especificada. Se você obtém este fracasso com frequência, considere aumentar a produção na coleção. Verifique as métricas do portal para ver se está a ter 429 erros. Reveja a sua chave de partição para garantir que resulta numa [distribuição uniforme do armazenamento e do volume de pedidos.](partition-data.md)

#### <a name="solution"></a>Solução:
1. Utilize o [portal ou o SDK](set-throughput.md) para aumentar a produção a provisionada.
1. Mude a base de dados ou o recipiente para [Autoscale.](provision-throughput-autoscale.md)

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).