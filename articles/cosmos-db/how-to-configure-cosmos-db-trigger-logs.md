---
title: Registos de Acionador de BD do Cosmos do Azure
description: Saiba como expor os registos de Acionador do Azure Cosmos DB para as suas funções do Azure log pipeline
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515082"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Como configurar e ler os logs de Acionador do Azure Cosmos DB

Este artigo descreve como configurar o ambiente de funções do Azure para enviar os registos de Acionador do Azure Cosmos DB para seu configurado [solução de monitorização](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Registos incluídos

O acionador do Azure Cosmos DB utiliza a [biblioteca de processador do Feed de alterações](./change-feed-processor.md) internamente, e a biblioteca gera um conjunto de registos de estado de funcionamento que podem ser utilizados para monitorizar operações internas para [resolução](./troubleshoot-changefeed-functions.md).

Os registos de estado de funcionamento descrevem como o acionador do Azure Cosmos DB se comporta ao tentar operações durante a cenários de balanceamento de carga ou de inicialização.

## <a name="enabling-logging"></a>Ativar o registo

Para ativar o registo de Acionador do Azure Cosmos DB, localize a `host.json` arquivo em seu projeto de funções do Azure ou a aplicação de funções do Azure e [configurar o nível de registo necessário](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Tem de ativar os rastreios para `Host.Triggers.CosmosDB` conforme mostrado no exemplo a seguir:

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

Depois da função do Azure é implementada com a configuração atualizada, irá ver os registos de Acionador do Azure Cosmos DB como parte da sua rastreios. Pode ver os registos no seu fornecedor de registo configurada sob o *categoria* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Os registos de consulta

Execute a seguinte consulta para consulta os registos gerados pelo acionador do Azure Cosmos DB no [do Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passos Seguintes

* [Ativar a monitorização](../azure-functions/functions-monitoring.md) nas suas aplicações de funções do Azure.
* Saiba como [diagnosticar e resolver problemas comuns de](./troubleshoot-changefeed-functions.md) ao utilizar o acionador do Azure Cosmos DB nas funções do Azure.