---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78190925"
---
Num projeto de biblioteca de classe C#, as encadernações são definidas como atributos vinculativos no método da função. O ficheiro *função.json* exigido pelas Funções é então gerado automaticamente com base nestes atributos.

Abra o ficheiro *HttpExample.cs* projeto e adicione `Run` o seguinte parâmetro à definição de método:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

O `msg` parâmetro é `ICollector<T>` um tipo, que representa uma coleção de mensagens que são escritas para uma ligação de saída quando a função completa. Neste caso, a saída é `outqueue`uma fila de armazenamento chamada . A cadeia de ligação para `StorageAccountAttribute`a conta de armazenamento é definida pelo . Este atributo indica a definição que contém a cadeia de ligação da conta de armazenamento e pode ser aplicada ao nível de classe, método ou parâmetro. Neste caso, pode omitir `StorageAccountAttribute` porque já está a usar a conta de armazenamento predefinida.

A definição do método executar deve agora parecer a seguinte:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
