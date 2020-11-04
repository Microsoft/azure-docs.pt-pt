---
title: Resolução de problemas Serviço DB da Azure Cosmos solicita exceções no tempo limite
description: Saiba como diagnosticar e corrigir o serviço DB da Azure Cosmos solicitar exceções no tempo limite.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 9e52acd2f7f594523059b22fefd121314bd77921
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340075"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnóstico e resolução de problemas Azure Cosmos DB solicitar exceções no tempo limite
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

AZure Cosmos DB devolveu um tempo limite de pedido HTTP 408.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para solicitar exceções no prazo de tempo.

### <a name="check-the-sla"></a>Verifique o SLA
Verifique [a monitorização DB da Azure Cosmos](monitor-cosmos-db.md) para ver se o número de 408 exceções viola o Azure Cosmos DB SLA.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Solução 1: Não violou o Azure Cosmos DB SLA
A aplicação deve lidar com este cenário e voltar a tentar estas falhas transitórias.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Solução 2: Violou o Azure Cosmos DB SLA
Contacte [o Suporte Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Chave de partição quente
AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Quando há uma partição quente, uma ou mais teclas de partição lógica numa partição física estão a consumir todas as Unidades de Pedido de Partição Física por segundo (RU/s). Ao mesmo tempo, o RU/S em outras divisórias físicas não está a ser utilizado. Como sintoma, o total de RU/s consumidos será inferior ao total previsto na base de dados ou no contentor. Você ainda vai ver estrangulamento (429s) sobre os pedidos contra a chave de partição lógica quente. Utilize a [métrica de consumo de RU normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho encontra uma divisória quente. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribui uniformemente o volume e armazenamento do pedido. Saiba como mudar a [sua tecla de partição.](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).