---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673274"
---
Agora, pode utilizar o novo `msg` parâmetro para escrever para a ligação de saída a partir do seu código de função. Adicione a seguinte linha de código antes da resposta de sucesso para adicionar o valor da `name` ligação de `msg` saída.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Quando utiliza uma ligação de saída, não precisa de utilizar o código SDK de armazenamento Azure para autenticação, obter uma referência de fila ou escrever dados. O tempo de funcionamento e a ligação de saída de fila das funções fazem essas tarefas para si.

O seu `run` método deve agora parecer o seguinte exemplo:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::