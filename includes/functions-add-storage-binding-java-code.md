---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 9d5bae1aedbd031a0a3c08ba5141aebc22f1c674
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493405"
---
Agora, pode utilizar o novo `msg` parâmetro para escrever para a ligação de saída a partir do seu código de função. Adicione a seguinte linha de código antes da resposta de sucesso para adicionar o valor da `name` ligação de `msg` saída.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Quando utiliza uma ligação de saída, não precisa de utilizar o código SDK de armazenamento Azure para autenticação, obter uma referência de fila ou escrever dados. O tempo de funcionamento e a ligação de saída de fila das funções fazem essas tarefas para si.

O seu `run` método deve agora parecer o seguinte exemplo:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-38":::