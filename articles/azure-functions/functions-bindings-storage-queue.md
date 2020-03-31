---
title: Gatilho de armazenamento de fila azure e encadernações para a visão geral das funções do Azure
description: Compreenda como utilizar o gatilho de armazenamento de fila Azure e a ligação de saída em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277313"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Gatilho de armazenamento de fila azure e encadernações para a visão geral das funções do Azure

As Funções Azure podem funcionar à medida que novas mensagens de armazenamento de fila Azure são criadas e podem escrever mensagens de fila dentro de uma função.

| Ação | Tipo |
|---------|---------|
| Executar uma função à medida que os dados de armazenamento da fila mudam | [Acionador](./functions-bindings-storage-queue-trigger.md) |
| Escreva mensagens de armazenamento de fila |[Encadernação de saída](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione à sua app Funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e encadernações requer que consulte o pacote apropriado. O pacote NuGet é utilizado para bibliotecas de classe .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicações.

| Idioma                                        | Adicione por...                                   | Observações 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalação do [pacote NuGet], versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registando o [pacote de extensão]          | Recomenda-se a [extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) para utilizar com o Código do Estúdio Visual. |
| C# Script (apenas online no portal Azure)         | Adicionar uma ligação                            | Para atualizar as extensões de ligação existentes sem ter de voltar a publicar a sua aplicação de funções, consulte [atualizar as suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualize as suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As funções 1.x apps têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)
- [Escreva mensagens de armazenamento de fila (encadernação de saída)](./functions-bindings-storage-queue-output.md)
