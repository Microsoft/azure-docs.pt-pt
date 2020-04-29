---
title: Ligações Azure Cosmos DB para funções 2.x
description: Compreenda como utilizar gatilhos e encadernações Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605771"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Gatilho e encadernação Azure Cosmos DB para funções azure 2.x visão geral

> [!div class="op_single_selector" title1="Selecione a versão do tempo de funcionamento das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este conjunto de artigos explica como trabalhar com ligações [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) em Funções Azure 2.x. A Azure Functions suporta ligações de gatilho, entrada e saída para o Azure Cosmos DB.

| Ação | Tipo |
|---------|---------|
| Executar uma função quando um documento Azure Cosmos DB é criado ou modificado | [Acionador](./functions-bindings-cosmosdb-v2-trigger.md) |
| Leia um documento da Azure Cosmos DB | [Encadernação de entrada](./functions-bindings-cosmosdb-v2-input.md) |
| Guardar alterações num documento Da Azure Cosmos DB  |[Encadernação de saída](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Esta referência é para [a versão 2.x das Funções Azure.](functions-versions.md)  Para obter informações sobre como utilizar estas ligações nas funções 1.x, consulte [ligações Azure Cosmos DB para funções Azure 1.x](functions-bindings-cosmosdb.md).
>
> Esta ligação foi originalmente chamada DocumentDB. Nas Funções versão 2.x, o gatilho, as encadernações e o pacote são todos chamados Cosmos DB.

## <a name="supported-apis"></a>APIs suportados

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [extensão Azure Tools] para utilizar com o Código do Estúdio Visual. |
| C# Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [atualizar as suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão de ferramentas azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As funções 1.x apps têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando um documento Azure Cosmos DB é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento Azure Cosmos DB (encadernação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar alterações num documento Azure Cosmos DB (Encadernação de saída)](./functions-bindings-cosmosdb-v2-output.md)
