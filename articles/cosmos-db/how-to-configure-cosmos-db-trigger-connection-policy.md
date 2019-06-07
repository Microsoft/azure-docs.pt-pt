---
title: Política de ligação do Azure Cosmos DB acionador
description: Saiba como configurar a política de ligação utilizada pelo acionador do Azure Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755333"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Como configurar a política de ligação utilizada pelo acionador do Azure Cosmos DB

Este artigo descreve como configurar a política de ligação ao utilizar o acionador do Azure Cosmos DB para ligar à sua conta do Cosmos do Azure.

## <a name="why-is-the-connection-policy-important"></a>Por que a diretiva de conexão é importante?

Existem dois modos de ligação - modo direto e o modo de Gateway. Para saber mais sobre esses modos de ligação, veja a [sugestões de desempenho](./performance-tips.md#networking) artigo. Por predefinição, **Gateway** é utilizado para estabelecer a todas as ligações no acionador do Azure Cosmos DB. No entanto, pode não ser a melhor opção para cenários orientados ao desempenho.

## <a name="changing-the-connection-mode-and-protocol"></a>Alterar o modo de ligação e protocolo

Existem duas definições de configuração da chave disponíveis para configurar a política de ligação de cliente – a **modo de ligação** e o **protocolo da ligação**. Pode alterar o modo de ligação predefinido e o protocolo utilizado, o acionador do Azure Cosmos DB e todos os [do Azure Cosmos DB enlaces](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Para alterar as definições predefinidas, tem de localizar o `host.json` do ficheiro no seu projeto de funções do Azure ou a aplicação de funções do Azure e adicione as seguintes [extra definição](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Em que `connectionMode` tem de ter o modo de conexão desejadas (direto ou Gateway) e `protocol` o protocolo de conexão desejadas (Tcp ou Https). 

Se o seu projeto de funções do Azure está a trabalhar com o tempo de execução do V1 de funções do Azure, a configuração tem uma diferença de nome de pequenas, deve usar `documentDB` em vez de `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Ao trabalhar com o plano de alojamento plano de consumo de funções do Azure, cada instância tem um limite na quantidade de conexões de soquete que possa manter. Ao trabalhar com Direct / modo TCP, por design mais ligações são criadas e pode atingir os [limite de plano de consumo](../azure-functions/manage-connections.md#connection-limit), caso em que pode utilizar o modo de Gateway ou executar as suas funções de Azure [o modo de serviço de aplicações](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Passos Seguintes

* [Limites de ligação nas funções do Azure](../azure-functions/manage-connections.md#connection-limit)
* [Sugestões de desempenho do Azure Cosmos DB](./performance-tips.md)
* [Exemplos de código](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
