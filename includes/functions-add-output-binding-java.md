---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673502"
---
Num projeto java, as ligações são definidas como anotações vinculativas no método de função. O *function.jsem* ficheiro é então autogerado com base nestas anotações.

Navegue até à localização do seu código de função em _src/main/java,_ abra o ficheiro de projeto *.java Função* e adicione o seguinte parâmetro à definição do `run` método:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

O `msg` parâmetro é um [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) tipo, que representa uma coleção de cordas que são escritas como mensagens para uma ligação de saída quando a função termina. Neste caso, a saída é uma fila de armazenamento chamada `outqueue` . O fio de ligação da conta de armazenamento é definido pelo `connection` método. Em vez da própria cadeia de ligação, passa-se a definição de aplicação que contém a cadeia de ligação da conta de armazenamento.

A `run` definição do método deve agora parecer o seguinte exemplo:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::