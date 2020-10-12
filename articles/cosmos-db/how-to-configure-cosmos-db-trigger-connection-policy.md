---
title: Azure Functions trigger para a política de conexão Cosmos DB
description: Saiba como configurar a política de ligação utilizada pelo gatilho de Funções Azure para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: df30a0ddca58e6d7bd74184fa7287df6818cfc37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84117152"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Como configurar a política de ligação utilizada pelo gatilho de Funções Azure para Cosmos DB

Este artigo descreve como pode configurar a política de ligação ao utilizar o gatilho Azure Functions para cosmos DB ligar à sua conta Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Porque é que a política de ligação é importante?

Existem dois modos de ligação - Modo direto e modo Gateway. Para saber mais sobre estes modos de ligação, consulte o artigo [dicas de desempenho.](./performance-tips.md#networking) Por padrão, **gateway** é usado para estabelecer todas as ligações no gatilho de Funções Azure para Cosmos DB. No entanto, pode não ser a melhor opção para cenários orientados para o desempenho.

## <a name="changing-the-connection-mode-and-protocol"></a>Alterar o modo de ligação e o protocolo

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
> Ao trabalhar com o plano de hospedagem do plano de consumo de funções Azure, cada instância tem um limite na quantidade de Ligações de Tomada que pode manter. Ao trabalhar com o modo Direct /TCP, por design são criadas mais ligações e podem atingir o limite do [Plano de Consumo,](../azure-functions/manage-connections.md#connection-limit)caso em que pode utilizar o modo Gateway ou executar as suas Funções Azure no [Modo de Serviço de Aplicações](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Passos seguintes

* [Limites de ligação nas funções Azure](../azure-functions/manage-connections.md#connection-limit)
* [Dicas de desempenho do Azure Cosmos DB](./performance-tips.md)
* [Amostras de código](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
