---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638098"
---
A chave de subscrição de recursos da Fala e a região são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneamente um objeto reconhecível de fala.

A instância de reconhecimento expõe várias formas de reconhecer a fala. Neste exemplo, a fala é reconhecida uma vez. Esta funcionalidade permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para deixar de reconhecer o discurso. Uma vez que o resultado é cessionado, o código escreverá o motivo de reconhecimento para a consola.

> [!TIP]
> O SDK do Discurso não `en-us` reconhecerá o uso para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.