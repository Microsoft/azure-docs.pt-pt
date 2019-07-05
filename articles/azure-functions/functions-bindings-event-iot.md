---
title: Enlaces de IoT Hub do Azure para as funções do Azure
description: Compreenda como utilizar os enlaces de IoT Hub nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e39a7b2c29ee541a9a104d681d25335496e5625b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480605"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Enlaces de IoT Hub do Azure para as funções do Azure

Este artigo explica como trabalhar com ligações de funções do Azure para o IoT Hub. O suporte do IoT Hub se baseia a [enlace dos Hubs de eventos do Azure](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Para a versão das funções do Azure 1.x, os enlaces de IoT Hub são fornecidos na [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Para as funções 2.x, utilize o [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) pacote, a versão 3.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Embora os exemplos de código seguinte utilizam a API do Hub de eventos, a sintaxe de determinado é aplicável para funções do IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
