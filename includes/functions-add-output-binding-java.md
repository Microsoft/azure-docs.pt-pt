---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673502"
---
Num projeto java, as encadernações são definidas como anotações vinculativas no método da função. O ficheiro *função.json* é então autogerado com base nestas anotações.

Navegue na localização do seu código de funções sob _o sRC/main/java,_ abra o `run` ficheiro do projeto *Function.java* e adicione o seguinte parâmetro à definição de método:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

O `msg` parâmetro é [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) um tipo, que representa uma coleção de cordas que são escritas como mensagens para uma ligação de saída quando a função completa. Neste caso, a saída é `outqueue`uma fila de armazenamento chamada . A cadeia de ligação para `connection` a conta de armazenamento é definida pelo método. Em vez da cadeia de ligação em si, passa a definição de aplicação que contém a cadeia de ligação da conta de armazenamento.

A `run` definição do método deve agora parecer o seguinte exemplo:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::