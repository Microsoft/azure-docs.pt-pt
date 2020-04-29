---
title: Gatilho de armazenamento Azure Blob e encadernações para funções azure
description: Aprenda a utilizar o gatilho de armazenamento Azure Blob e encadernações em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277235"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Encadernações de armazenamento Azure Blob para visão geral das Funções Azure

A Azure Functions integra-se com o [Armazenamento Azure](https://docs.microsoft.com/azure/storage/) através de [gatilhos e encadernações.](./functions-triggers-bindings.md) Integrar-se com o armazenamento Blob permite-lhe construir funções que reagem a alterações nos dados blob, bem como valores de leitura e escrita.

| Ação | Tipo |
|---------|---------|
| Executar uma função como alterações de dados de armazenamento blob | [Acionador](./functions-bindings-storage-blob-trigger.md) |
| Leia os dados de armazenamento blob numa função | [Encadernação de entrada](./functions-bindings-storage-blob-input.md) |
| Permitir que uma função escreva dados de armazenamento de bolhas |[Encadernação de saída](./functions-bindings-storage-blob-output.md) |

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

- [Executar uma função quando os dados de armazenamento blob mudar](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento de blob quando uma função funciona](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento blob a partir de uma função](./functions-bindings-storage-blob-output.md)
