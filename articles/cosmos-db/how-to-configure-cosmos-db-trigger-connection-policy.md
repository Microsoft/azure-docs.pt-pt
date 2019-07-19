---
title: Gatilho de Azure Functions para a política de conexão Cosmos DB
description: Saiba como configurar a política de conexão usada pelo gatilho Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 359b6a905e64046aad62b70ae53b993c86884ad2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335638"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Como configurar a política de conexão usada pelo gatilho Azure Functions para Cosmos DB

Este artigo descreve como você pode configurar a política de conexão ao usar o gatilho de Azure Functions para Cosmos DB para se conectar à sua conta do Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Por que a política de conexão é importante?

Há dois modos de conexão: modo direto e modo de gateway. Para saber mais sobre esses modos de conexão, consulte o artigo [dicas de desempenho](./performance-tips.md#networking) . Por padrão, o **Gateway** é usado para estabelecer todas as conexões no gatilho Azure Functions para Cosmos DB. No entanto, pode não ser a melhor opção para cenários controlados por desempenho.

## <a name="changing-the-connection-mode-and-protocol"></a>Alterando o modo de conexão e o protocolo

Há duas definições de configuração principais disponíveis para configurar a política de conexão do cliente – o **modo de conexão** e o **protocolo de conexão**. Você pode alterar o modo de conexão padrão e o protocolo usado pelo gatilho de Azure Functions para Cosmos DB e todas as [associações de Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Para alterar as configurações padrão, você precisa localizar o `host.json` arquivo em seu projeto de Azure Functions ou Azure Functions aplicativo e adicionar a seguinte [configuração extra](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Onde `connectionMode` o deve ter o modo de conexão desejado (direto ou gateway `protocol` ) e o protocolo de conexão desejado (TCP ou HTTPS). 

Se seu projeto de Azure Functions estiver trabalhando com o tempo de execução do Azure Functions v1, a configuração terá uma pequena diferença `documentDB` de nome `cosmosDB`, que deverá ser usada em vez de:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Ao trabalhar com o plano de Hospedagem de plano de consumo Azure Functions, cada instância tem um limite na quantidade de conexões de soquete que ele pode manter. Ao trabalhar com o modo direto/TCP, o design de mais conexões é criado e pode atingir o [limite do plano de consumo](../azure-functions/manage-connections.md#connection-limit). nesse caso, você pode usar o modo de gateway ou executar o Azure Functions no modo de serviço de [aplicativo](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Passos Seguintes

* [Limites de conexão no Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB dicas de desempenho](./performance-tips.md)
* [Exemplos de código](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
