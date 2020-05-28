---
title: Funções Azure disparam para a política de conexão Cosmos DB
description: Saiba como configurar a política de ligação utilizada pelo gatilho de Funções Azure para cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: df30a0ddca58e6d7bd74184fa7287df6818cfc37
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117152"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Como configurar a política de ligação utilizada pelo gatilho das Funções Azure para cosmos DB

Este artigo descreve como pode configurar a política de ligação ao utilizar o gatilho das Funções Azure para o Cosmos DB ligar-se à sua conta Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Por que a política de ligação é importante?

Existem dois modos de ligação - modo direto e modo Gateway. Para saber mais sobre estes modos de ligação, consulte o artigo de dicas de [desempenho.](./performance-tips.md#networking) Por padrão, **gateway** é usado para estabelecer todas as ligações no gatilho funções Azure para Cosmos DB. No entanto, pode não ser a melhor opção para cenários orientados para o desempenho.

## <a name="changing-the-connection-mode-and-protocol"></a>Alterar o modo de ligação e o protocolo

Existem duas definições de configuração chave disponíveis para configurar a política de ligação ao cliente – o modo de **ligação** e o protocolo de **ligação**. Pode alterar o modo de ligação padrão e o protocolo utilizado pelo gatilho funções Azure para cosmos DB e todas as [ligações Azure Cosmos DB).](../azure-functions/functions-bindings-cosmosdb-v2-output.md) Para alterar as definições predefinidas, é necessário localizar o `host.json` ficheiro no projeto funções do Azure ou na App de Funções Azure e adicionar a seguinte [definição extra:](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Onde deve ter o modo de `connectionMode` ligação desejado (Direto ou Gateway) e o protocolo de `protocol` ligação desejado (Tcp ou Https). 

Se o seu projeto Funções Azure estiver a trabalhar com o tempo de funcionamento das Funções Azure V1, a configuração tem uma ligeira diferença de nome, deve utilizar `documentDB` em vez `cosmosDB` de:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Ao trabalhar com o plano de hospedagem do Plano de Consumo de Funções Azure, cada instância tem um limite na quantidade de Ligações de Tomada que pode manter. Ao trabalhar com o modo Direct /TCP, através do design são criadas mais ligações e pode atingir o limite do Plano de [Consumo,](../azure-functions/manage-connections.md#connection-limit)caso em que pode utilizar o modo Gateway ou executar as suas Funções Azure no modo de serviço de [aplicações](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Próximos passos

* [Limites de ligação nas funções do Azure](../azure-functions/manage-connections.md#connection-limit)
* [Dicas de desempenho do Azure Cosmos DB](./performance-tips.md)
* [Amostras de código](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
