---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739824"
---
Como o arquétipo também cria um conjunto de testes, é necessário atualizar estes testes para lidar com o novo `msg` parâmetro na assinatura do `run` método.  

Navegue pela localização do seu código de teste sob _src/test/java,_ abra o ficheiro de projeto *.java Função* e substitua a linha de código `//Invoke` pelo seguinte código.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
