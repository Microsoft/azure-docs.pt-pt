---
title: Configure e leia registos com gatilho de funções Azure para Cosmos DB
description: Saiba como expor os registos ao pipeline de registo de funções Azure ao utilizar o gatilho de Funções Azure para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 31c9f9d94acab7322cf11d084e4010f2defd6f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261703"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Como configurar e ler os registos ao utilizar o gatilho de Funções Azure para Cosmos DB

Este artigo descreve como pode configurar o seu ambiente de Funções Azure para enviar o gatilho de Funções Azure para os registos de DB do Cosmos para a sua [solução de monitorização](../azure-functions/functions-monitoring.md)configurada .

## <a name="included-logs"></a>Registos incluídos

O gatilho de funções Azure para Cosmos DB utiliza internamente a [Biblioteca do Processador de Feed de Alteração,](./change-feed-processor.md) e a biblioteca gera um conjunto de registos de saúde que podem ser utilizados para monitorizar operações internas para efeitos de [resolução de problemas](./troubleshoot-changefeed-functions.md).

Os registos de saúde descrevem como o gatilho das Funções Azure para o Cosmos DB se comporta ao tentar operações durante cenários de equilíbrio de carga ou de inicialização.

## <a name="enabling-logging"></a>Habilitação de exploração madeireira

Para ativar o registo ao utilizar o gatilho de Funções Azure para Cosmos DB, localizar o `host.json` ficheiro no seu projeto Azure Functions ou app Azure Functions e [configurar o nível de registo necessário](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Tem de ativar os  `Host.Triggers.CosmosDB` vestígios, como mostrado na amostra seguinte:

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

Depois de a Função Azure ser implementada com a configuração atualizada, verá o gatilho de Funções Azure para registos DeSB cosmos como parte dos seus vestígios. Pode visualizar os registos no seu fornecedor de registos configurados na *categoria* `Host.Triggers.CosmosDB` .

## <a name="query-the-logs"></a>Consultar os troncos

Executar a seguinte consulta para consultar os registos gerados pelo gatilho Azure Functions para Cosmos DB in [Azure Application Insights' Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passos seguintes

* [Ativar a monitorização](../azure-functions/functions-monitoring.md) nas suas aplicações Azure Functions.
* Aprenda a [diagnosticar e resolver problemas comuns](./troubleshoot-changefeed-functions.md) ao utilizar o gatilho de Funções Azure para Cosmos DB.
