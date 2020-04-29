---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673274"
---
Agora, pode usar `msg` o novo parâmetro para escrever para a ligação de saída a partir do seu código de função. Adicione a seguinte linha de código antes da `name` resposta `msg` de sucesso para adicionar o valor da ligação de saída.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Quando utiliza uma ligação de saída, não é necessário utilizar o código SDK de Armazenamento Azure para autenticação, obtendo uma referência de fila ou escrevendo dados. O tempo de funcionamento das Funções e a ligação de saída da fila fazem essas tarefas para si.

O `run` seu método deve agora parecer o seguinte exemplo:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::