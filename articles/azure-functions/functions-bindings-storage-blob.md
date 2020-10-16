---
title: Gatilho de armazenamento Azure Blob e encadernações para funções Azure
description: Aprenda a utilizar o gatilho de armazenamento Azure Blob e as ligações em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104364"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Encadernações de armazenamento Azure Blob para visão geral das funções Azure

As funções Azure integram-se com [o Azure Storage](../storage/index.yml) através [de gatilhos e encadernações](./functions-triggers-bindings.md). A integração com o armazenamento Blob permite-lhe construir funções que reagem a alterações nos dados blob, bem como a ler e escrever valores.

| Ação | Tipo |
|---------|---------|
| Executar uma função como alterações de dados de armazenamento de blob | [Acionador](./functions-bindings-storage-blob-trigger.md) |
| Leia os dados de armazenamento de bolhas numa função | [Encadernação de entrada](./functions-bindings-storage-blob-input.md) |
| Permitir uma função para escrever dados de armazenamento de bolhas |[Ligação de saída](./functions-bindings-storage-blob-output.md) |

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

- [Executar uma função quando os dados de armazenamento blob mudam](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento de bolhas quando uma função é executado](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento de bolhas a partir de uma função](./functions-bindings-storage-blob-output.md)