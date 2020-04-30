---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673466"
---
## <a name="update-the-tests"></a>Atualizar os testes

Como o arquétipo também cria um conjunto de testes, `msg` é necessário `run` atualizar estes testes para lidar com o novo parâmetro na assinatura do método.  

Navegue na localização do seu código de teste sob _o sRC/teste/java,_ abra o `//Invoke` ficheiro do projeto *Function.java* e substitua a linha de código sob o seguinte código.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
