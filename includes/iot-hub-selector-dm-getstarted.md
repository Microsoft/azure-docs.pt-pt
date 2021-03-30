---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "69558716"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

As aplicações back-end podem utilizar primitivos Azure IoT Hub, como [o dispositivo twin][lnk-devtwin] e [métodos diretos,][lnk-c2dmethod]para iniciar e monitorizar remotamente as ações de gestão de dispositivos em dispositivos. Este tutorial mostra-lhe como uma aplicação back-end e uma aplicação de dispositivo podem trabalhar em conjunto para iniciar e monitorizar um reboot de dispositivo remoto usando o IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilize um método direto para iniciar ações de gestão de dispositivos (como reiniciar, reiniciar a fábrica e atualizar firmware) a partir de uma aplicação de back-end na nuvem. O dispositivo é responsável por:

* Manuseando o pedido de método enviado do IoT Hub.

* Iniciar a ação específica do dispositivo correspondente no dispositivo.

* Fornecendo atualizações de estado através *de propriedades reportadas* ao IoT Hub.

Pode utilizar uma aplicação de back-end na nuvem para executar consultas de twin do dispositivo para relatar o progresso das suas ações de gestão de dispositivos.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
