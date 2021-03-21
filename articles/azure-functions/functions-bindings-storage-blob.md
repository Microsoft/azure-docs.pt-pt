---
title: Gatilho de armazenamento Azure Blob e encadernações para funções Azure
description: Aprenda a utilizar o gatilho de armazenamento Azure Blob e as ligações em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381533"
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

#### <a name="storage-extension-5x-and-higher"></a>Extensão de armazenamento 5.x e superior

Uma nova versão da extensão de encadernações de armazenamento está disponível como um [pacote NuGet de pré-visualização](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Esta pré-visualização introduz a capacidade de [se conectar usando uma identidade em vez de um segredo](./functions-reference.md#configure-an-identity-based-connection). Para aplicações .NET, também altera os tipos a que se pode ligar, substituindo os tipos de `WindowsAzure.Storage` e por tipos mais recentes de `Microsoft.Azure.Storage` [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs).

> [!NOTE]
> O pacote de pré-visualização não está incluído num pacote de extensão e deve ser instalado manualmente. Para aplicações .NET, adicione uma referência ao pacote. Para todos os outros tipos de aplicações, consulte [Update your extensions].

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualize as suas extensões]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

As aplicações Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.jsnas definições

> [!NOTE]
> Esta secção não se aplica quando se utiliza versões de extensão antes do ponto 5.0.0. Para estas versões, não existem configurações globais para bolhas.

Esta secção descreve as definições de configuração global disponíveis para esta ligação quando se utiliza a [versão de extensão 5.0.0 e superior](#storage-extension-5x-and-higher). O exemplo *host.jsno* ficheiro abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as definições de configuração global nas versões 2.x e não só, consulte [host.jsem referência para Funções Azure](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|maxDegreeOfParallelismo|8 * (número de núcleos disponíveis)|O número inteiro de invocações simultâneas permitiu cada função desencadeada por bolhas. O valor mínimo permitido é 1.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando os dados de armazenamento blob mudam](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento de bolhas quando uma função é executado](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento de bolhas a partir de uma função](./functions-bindings-storage-blob-output.md)
