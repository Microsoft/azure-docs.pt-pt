---
title: Azure Functions trigger para a configuração avançada do Cosmos DB
description: Saiba como configurar a política de registo e conexão utilizada pelo gatilho de Funções Azure para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 30328db465e0d9bf8c1ce67d92e48c688c51e043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574621"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Como configurar a madeira e a conectividade com o gatilho das Funções Azure para o Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve opções de configuração avançadas que pode definir ao utilizar o gatilho Azure Functions para Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Ativar registos específicos do gatilho

O gatilho de funções Azure para Cosmos DB utiliza internamente a [Biblioteca do Processador de Feed de Alteração,](./change-feed-processor.md) e a biblioteca gera um conjunto de registos de saúde que podem ser utilizados para monitorizar operações internas para efeitos de [resolução de problemas](./troubleshoot-changefeed-functions.md).

Os registos de saúde descrevem como o gatilho das Funções Azure para o Cosmos DB se comporta ao tentar operações durante cenários de equilíbrio de carga ou de inicialização.

### <a name="enabling-logging"></a>Habilitação de exploração madeireira

Para ativar o registo ao utilizar o gatilho de Funções Azure para Cosmos DB, localizar o `host.json` ficheiro no seu projeto Azure Functions ou app Azure Functions e [configurar o nível de registo necessário](../azure-functions/functions-monitoring.md#log-levels-and-categories). Ativar os vestígios para  `Host.Triggers.CosmosDB` os seguintes vestígios:

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

### <a name="query-the-logs"></a>Consultar os troncos

Executar a seguinte consulta para consultar os registos gerados pelo gatilho Azure Functions para Cosmos DB in [Azure Application Insights' Analytics](../azure-monitor/logs/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Configurar a política de ligação

Existem dois modos de ligação - Modo direto e modo Gateway. Para saber mais sobre estes modos de ligação, consulte o artigo [modos de ligação.](sql-sdk-connection-modes.md) Por padrão, **gateway** é usado para estabelecer todas as ligações no gatilho de Funções Azure para Cosmos DB. No entanto, pode não ser a melhor opção para cenários orientados para o desempenho.

### <a name="changing-the-connection-mode-and-protocol"></a>Alterar o modo de ligação e o protocolo

Existem duas definições de configuração chave disponíveis para configurar a política de ligação do cliente - o **modo de ligação** e o **protocolo de ligação**. Pode alterar o modo de ligação predefinido e o protocolo utilizado pelo gatilho Azure Functions para Cosmos DB e todas as [ligações DB do Cosmos Azure](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Para alterar as definições predefinidas, é necessário localizar o `host.json` ficheiro no seu projeto Azure Functions ou app Azure Functions e adicionar a seguinte [definição extra](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Onde `connectionMode` deve ter o modo de ligação pretendido (Direct ou Gateway) e o protocolo de `protocol` ligação pretendido (Tcp ou Https). 

Se o seu projeto Azure Functions estiver a trabalhar com o tempo de execução Azure Functions V1, a configuração tem uma ligeira diferença de nome, deve utilizar `documentDB` em vez `cosmosDB` de:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Ao hospedar a sua aplicação de função num plano de consumo, cada instância tem um limite na quantidade de Ligações de Tomada que pode manter. Ao trabalhar com o modo Direct /TCP, por design são criadas mais ligações e podem atingir o limite do [plano de consumo,](../azure-functions/manage-connections.md#connection-limit)caso em que pode utilizar o modo Gateway ou, em vez disso, hospedar a sua aplicação de função num [plano Premium](../azure-functions/functions-premium-plan.md) ou num plano [dedicado (Serviço de Aplicações).](../azure-functions/dedicated-plan.md)

## <a name="next-steps"></a>Passos seguintes

* [Limites de ligação nas funções Azure](../azure-functions/manage-connections.md#connection-limit)
* [Ativar a monitorização](../azure-functions/functions-monitoring.md) nas suas aplicações Azure Functions.
* Aprenda a [diagnosticar e resolver problemas comuns](./troubleshoot-changefeed-functions.md) ao utilizar o gatilho de Funções Azure para Cosmos DB.