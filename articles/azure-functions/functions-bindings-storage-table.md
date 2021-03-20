---
title: Encadernações de armazenamento de mesa Azure para funções Azure
description: Compreenda como utilizar as ligações de armazenamento da tabela Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096730"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Encadernações de armazenamento de mesa Azure para funções Azure

As funções Azure integram-se com [o Azure Storage](../storage/index.yml) através [de gatilhos e encadernações](./functions-triggers-bindings.md). A integração com o armazenamento de mesa permite-lhe construir funções que lêem e escrevem dados de armazenamento de mesa.

| Ação | Tipo |
|---------|---------|
| Leia os dados de armazenamento de mesa numa função | [Encadernação de entrada](./functions-bindings-storage-table-input.md) |
| Permitir uma função para escrever dados de armazenamento de mesa |[Ligação de saída](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

As ligações de armazenamento da tabela são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, versão 3.x. O código-fonte para o pacote está no [repositório azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações de armazenamento da tabela são fornecidas no pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x. O código-fonte para o pacote está no [repositório azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passos seguintes

- [Leia os dados de armazenamento de mesa quando uma função é executado](./functions-bindings-storage-table-input.md)
- [Escreva dados de armazenamento de mesa a partir de uma função](./functions-bindings-storage-table-output.md)
