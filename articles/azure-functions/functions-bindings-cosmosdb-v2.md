---
title: Ligações DB Azure Cosmos para funções 2.xd e superior
description: Entenda como usar os gatilhos e encadernações DB do Azure Cosmos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106931"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB gatilho e encadernações para funções Azure 2.x e visão geral superior

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2 e superior](functions-bindings-cosmosdb-v2.md)

Este conjunto de artigos explica como trabalhar com as ligações [DB da Azure Cosmos](../cosmos-db/serverless-computing-database.md) em Azure Functions 2.x e superior. As funções Azure suportam o gatilho, a entrada e as ligações de saída para a Azure Cosmos DB.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um documento DB Azure Cosmos é criado ou modificado | [Acionador](./functions-bindings-cosmosdb-v2-trigger.md) |
| Leia um documento DB de Azure Cosmos | [Encadernação de entrada](./functions-bindings-cosmosdb-v2-input.md) |
| Guardar alterações num documento DB da Azure Cosmos  |[Ligação de saída](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Esta referência destina-se à [versão 2.x das Funções Azure .](functions-versions.md)  Para obter informações sobre como utilizar estas ligações em Funções 1.x, consulte [as ligações DB do Azure Cosmos para as Funções Azure 1.x](functions-bindings-cosmosdb.md).
>
> Esta ligação foi originalmente chamada DocumentDB. Na versão 2.x e superior das Funções, o gatilho, as encadernações e o pacote são todos chamados Cosmos DB.

## <a name="supported-apis"></a>APIs suportadas

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools] com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./functions-bindings-register.md
[Extensão de Ferramentas Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando um documento DB Azure Cosmos é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento DB da Azure Cosmos (ligação à entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar alterações num documento DB do Azure Cosmos (ligação à saída)](./functions-bindings-cosmosdb-v2-output.md)