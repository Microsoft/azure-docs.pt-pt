---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78190925"
---
Num projeto de biblioteca de classes C', as ligações são definidas como atributos de ligação no método de função. A *function.jsno* ficheiro exigido por Funções é então gerada automaticamente com base nestes atributos.

Abra o ficheiro *HttpExample.cs* do projeto e adicione o seguinte parâmetro à definição do `Run` método:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são escritas para uma ligação de saída quando a função termina. Neste caso, a saída é uma fila de armazenamento chamada `outqueue` . O fio de ligação da conta de armazenamento é definido pela `StorageAccountAttribute` . Este atributo indica a definição que contém a cadeia de ligação da conta de armazenamento e pode ser aplicada ao nível da classe, método ou parâmetro. Neste caso, pode omitir `StorageAccountAttribute` porque já está a utilizar a conta de armazenamento predefinida.

A definição do método Run deve agora parecer-se com o seguinte:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
