---
title: Resolução de problemas Serviço DB da Azure Cosmos solicitam exceção de tempo limite
description: Como diagnosticar e corrigir o serviço de serviço da Cosmos DB pedir exceção no tempo limite
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294530"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnóstico e resolução de problemas Azure Cosmos DB solicitar tempo limite
Azure Cosmos DB devolveu um tempo limite de pedido HTTP 408

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para solicitar exceções no prazo de tempo.

### <a name="1-check-the-sla"></a>1. Verifique o SLA
O cliente deve verificar a [monitorização DB da Azure Cosmos](monitor-cosmos-db.md) para verificar se as exceções número 408 violam o Cosmos DB SLA.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Solução 1: Não violou o Cosmos DB SLA
A aplicação deve lidar com este cenário e voltar a tentar estas falhas transitórias.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Solução 2: Violou o Cosmos DB SLA
Contacte o Suporte Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. Chave de partição quente
AZure Cosmos DB distribui a produção global a provisionada uniformemente através de divisórias físicas. Quando há uma partição quente, uma ou mais teclas de partição lógica numa partição física estão a consumir todos os RU/s da partição física, enquanto o RU/s em outras divisórias físicas não é usedado. Como sintoma, o total de RU/s consumidos será inferior ao TOTAL previsto ru/s na base de dados ou contentor, mas ainda verá estrangulamento (429s) nos pedidos contra a chave de partição lógica quente. Utilize a [métrica de consumo de RU normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho encontra uma divisória quente. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribui uniformemente o volume e armazenamento do pedido. Saiba como mudar a [sua tecla de partição.](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)