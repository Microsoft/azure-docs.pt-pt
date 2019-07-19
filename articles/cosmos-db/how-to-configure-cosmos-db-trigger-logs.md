---
title: Configurar e ler logs ao usar o gatilho Azure Functions para Cosmos DB
description: Saiba como expor os logs para o pipeline de log de Azure Functions ao usar o gatilho Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: ad53762ad490c5e8feedb83007b8721fe4883806
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334525"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Como configurar e ler os logs ao usar o gatilho Azure Functions para Cosmos DB

Este artigo descreve como você pode configurar seu ambiente de Azure Functions para enviar o gatilho de Azure Functions para logs de Cosmos DB para sua [solução de monitoramento](../azure-functions/functions-monitoring.md)configurada.

## <a name="included-logs"></a>Logs incluídos

O gatilho de Azure Functions para Cosmos DB usa a [biblioteca do processador do feed de alterações](./change-feed-processor.md) internamente, e a biblioteca gera um conjunto de logs de integridade que podem ser usados para monitorar operações internas para fins de solução de [problemas](./troubleshoot-changefeed-functions.md).

Os logs de integridade descrevem como o gatilho de Azure Functions para Cosmos DB se comporta ao tentar operações durante os cenários de balanceamento de carga ou inicialização.

## <a name="enabling-logging"></a>Habilitando o log

Para habilitar o log ao usar o gatilho de Azure Functions para Cosmos DB `host.json` , localize o arquivo em seu projeto de Azure Functions ou aplicativo de Azure Functions e [Configure o nível de registro em log necessário](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Você precisa habilitar os rastreamentos para `Host.Triggers.CosmosDB` , conforme mostrado no exemplo a seguir:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Depois que a função do Azure for implantada com a configuração atualizada, você verá o gatilho Azure Functions para Cosmos DB logs como parte de seus rastreamentos. Você pode exibir os logs em seu provedor de log configurado na *categoria* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Consultar os logs

Execute a consulta a seguir para consultar os logs gerados pelo gatilho Azure Functions para Cosmos DB na [análise do aplicativo Azure](../azure-monitor/app/analytics.md)insights:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passos Seguintes

* [Habilite o monitoramento](../azure-functions/functions-monitoring.md) em seus aplicativos Azure functions.
* Saiba como [diagnosticar e solucionar problemas comuns](./troubleshoot-changefeed-functions.md) ao usar o gatilho de Azure Functions para Cosmos DB.