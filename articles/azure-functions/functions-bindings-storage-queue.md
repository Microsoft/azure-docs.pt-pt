---
title: Gatilho de armazenamento de fila Azure e encadernações para visão geral das funções Azure
description: Compreenda como utilizar o gatilho de armazenamento da fila Azure e a ligação de saída em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2d8f3985bc9a726735ebc0af0a3d3422f4fca54a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108869"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Gatilho de armazenamento de fila Azure e encadernações para visão geral das funções Azure

As funções Azure podem funcionar à medida que forem criadas novas mensagens de armazenamento da Fila Azure e podem escrever mensagens de fila dentro de uma função.

| Ação | Tipo |
|---------|---------|
| Executar uma função à medida que os dados de armazenamento de fila mudam | [Acionador](./functions-bindings-storage-queue-trigger.md) |
| Escreva mensagens de armazenamento de fila |[Ligação de saída](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua aplicação Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as ligações requer que você refira o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é utilizado para todos os outros tipos de aplicação.

| Linguagem                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registar o [pacote de extensão]          | Recomenda-se a utilização da [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) com Código de Estúdio Visual. |
| C# Script (online-only in Azure portal)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de republicar a sua aplicação de função, consulte [Atualizar as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualize as suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)
- [Escreva mensagens de armazenamento de fila (ligação de saída)](./functions-bindings-storage-queue-output.md)