---
title: Configure e leia registos com funções azure gatilho para Cosmos DB
description: Aprenda a expor os registos ao oleoduto de exploração de funções Azure ao utilizar o gatilho de Funções Azure para cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75441835"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Como configurar e ler os registos ao utilizar funções azure gatilho para Cosmos DB

Este artigo descreve como pode configurar o ambiente das Funções Azure para enviar o gatilho de Funções Azure para os registos cosmos DB para a sua solução de [monitorização](../azure-functions/functions-monitoring.md)configurada .

## <a name="included-logs"></a>Registos incluídos

O gatilho de funções Azure para cosmos DB utiliza internamente a Biblioteca do [Processador change feed,](./change-feed-processor.md) e a biblioteca gera um conjunto de registos de saúde que podem ser usados para monitorizar operações internas para [fins de resolução](./troubleshoot-changefeed-functions.md)de problemas .

Os registos de saúde descrevem como o gatilho das Funções Azure para cosmos DB se comporta quando se tenta operações durante cenários de equilíbrio de carga ou inicialização.

## <a name="enabling-logging"></a>Habilitar a exploração madeireira

Para ativar o registo ao utilizar o gatilho `host.json` das Funções Azure para o Cosmos DB, localize o ficheiro no projeto das Funções Azure ou na App funções Azure e [configure o nível de registo necessário](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Tem de ativar os `Host.Triggers.CosmosDB` vestígios, como mostrado na seguinte amostra:

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

Depois de a Função Azure ser implementada com a configuração atualizada, verá o gatilho das Funções Azure para os registos Db cosmos como parte dos seus vestígios. Pode visualizar os registos do seu fornecedor de registos configurado na *categoria* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Consulta dos registos

Faça a seguinte consulta para consultar os registos gerados pelo gatilho das Funções Azure para cosmos DB em [Azure Application Insights' Analytics:](../azure-monitor/app/analytics.md)

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passos seguintes

* [Ative a monitorização](../azure-functions/functions-monitoring.md) nas aplicações das funções Azure.
* Aprenda a [diagnosticar e resolver problemas comuns](./troubleshoot-changefeed-functions.md) ao usar o gatilho das Funções Azure para cosmos DB.