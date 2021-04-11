---
title: Quickstart - Gerir números de telefone utilizando serviços de comunicação Azure
description: Saiba como gerir os números de telefone utilizando os Serviços de Comunicação Azure
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 0f4a461ac5d459c6e3311400785e34bc22f40a00
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728458"
---
# <a name="quickstart-manage-phone-numbers"></a>Quickstart: Gerir números de telefone

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

Questões e questões comuns:

- A compra de telefone é suportada apenas nos EUA. Para comprar números de telefone, certifique-se de que:
  - O endereço de faturação de subscrição Azure associado está localizado nos Estados Unidos. Não é possível mover um recurso para outra subscrição neste momento.
  - O seu recurso de Serviços de Comunicação é aprovisionado na localização de dados dos Estados Unidos. Não é possível mover um recurso para outro local de dados neste momento.

- Quando um número de telefone é libertado, o número de telefone não será libertado ou poderá ser recomprado até ao final do ciclo de faturação.

- Quando um recurso de Serviços de Comunicação é eliminado, os números de telefone associados a esse recurso serão automaticamente libertados ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido aprendeu a:

> [!div class="checklist"]
> * Comprar um número de telefone
> * Gerencie o seu número de telefone
> * Liberte um número de telefone

> [!div class="nextstepaction"]
> [Enviar um SMS](../telephony-sms/send.md) 
>  [Começa com a chamada](../voice-video-calling/getting-started-with-calling.md)
