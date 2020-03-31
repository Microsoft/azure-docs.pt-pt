---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201942"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Durante o arranque, o anfitrião descarrega e instala a [extensão de ligação](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) de Armazenamento e outras extensões de ligação da Microsoft. Esta instalação ocorre porque as extensões de ligação são ativadas por padrão no ficheiro *host.json* com as seguintes propriedades:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se encontrar erros relacionados com extensões de ligação, verifique se as propriedades acima estão presentes no *host.json*.
::: zone-end  