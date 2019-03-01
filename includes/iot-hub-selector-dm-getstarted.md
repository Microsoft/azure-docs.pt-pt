---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011820"
---
> [!div class="op_single_selector"]
> * [Dispositivo: NODE. js serviço: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Dispositivo: C#Serviço: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Dispositivo: Serviço de Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Dispositivo: Serviço de Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Aplicações de back-end podem utilizar primitivos do IoT Hub do Azure, como [dispositivo duplo] [ lnk-devtwin] e [métodos diretos][lnk-c2dmethod], para iniciar e monitorizar o dispositivo remotamente ações de gestão nos dispositivos. Este tutorial mostra-lhe como uma aplicação de back-end e uma aplicação de dispositivo podem trabalhar em conjunto para iniciar e monitorizar um reinício do dispositivo remoto através do IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilize um método direto para iniciar ações de gestão de dispositivos (como o reinício, reposição de fábrica e atualização de firmware) a partir de uma aplicação de back-end na cloud. O dispositivo é responsável por:

* Manipulando a solicitação de método enviada a partir do IoT Hub.
* A iniciar a ação de dispositivo específicos correspondente no dispositivo.
* Fornecendo atualizações de estado através de *propriedades comunicadas* ao IoT Hub.

Pode utilizar uma aplicação de back-end na cloud para executar consultas twin do dispositivo para comunicar o progresso da suas ações de gestão do dispositivo.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
