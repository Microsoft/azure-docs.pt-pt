---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400793"
---
A chave de subscrição de recursos da Fala e a região são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneamente um objeto reconhecível de fala.

A instância de reconhecimento expõe várias formas de reconhecer a fala. Neste exemplo, a fala é reconhecida uma vez. Esta funcionalidade permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para deixar de reconhecer o discurso. Uma vez que o resultado é cessionado, o código escreverá o motivo de reconhecimento para a consola.

> [!TIP]
> O SDK do Discurso não `en-us` reconhecerá o uso para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.