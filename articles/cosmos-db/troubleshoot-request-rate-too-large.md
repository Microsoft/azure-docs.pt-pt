---
title: Resolução de problemas Azure Cosmos DB taxa de pedido para grande exceção
description: Como diagnosticar e fixar taxa de pedido para grande exceção
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294523"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnosticar e resolver problemas Cosmos DB muitos pedidos
A taxa de pedido é demasiado grande ou o código de erro 429 indica que os seus pedidos estão a ser estrangulados.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para muitos pedidos.

### <a name="1-check-the-metrics"></a>1. Verifique as métricas
O cliente deve verificar a [monitorização DB da Azure Cosmos](monitor-cosmos-db.md) para verificar se o número 429 exceções.

## <a name="cause"></a>Causa:
A produção consumida (RU/s) excedeu a [produção prevista](set-throughput.md). O SDK redorá automaticamente os pedidos com base na política de retíria especificada. Se você obtém este fracasso com frequência, considere aumentar a produção na coleção. Verifique as métricas do portal para ver se está a obter 429 erros. Reveja a sua chave de partição para garantir que resulta numa [distribuição uniforme do armazenamento e do volume de pedidos.](partition-data.md)

## <a name="solution"></a>Solução:
1. Utilize o [portal ou o SDK](set-throughput.md) para aumentar a produção a provisionada.
2. Mude a base de dados ou o recipiente para [Autoscale.](provision-throughput-autoscale.md)

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)