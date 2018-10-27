---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 13eddced155eab6dedfbce77330e7a178ecfb3cb
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166469"
---
> [!div class="op_single_selector"]
> * [Dispositivo: Node.js Serviço: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Dispositivo: C# serviço:C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
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
