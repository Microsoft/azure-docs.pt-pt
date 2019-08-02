---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 60c0c700dde5afda2ca93a92b334cbc4f0134043
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667963"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Os aplicativos de back-end podem usar primitivos do Hub IoT do Azure, como [dispositivos][lnk-devtwin] e [métodos diretos][lnk-c2dmethod], para iniciar remotamente e monitorar ações de gerenciamento de dispositivos em dispositivos. Este tutorial mostra como um aplicativo de back-end e um aplicativo de dispositivo podem trabalhar juntos para iniciar e monitorar uma reinicialização remota de dispositivo usando o Hub IoT.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use um método direto para iniciar ações de gerenciamento de dispositivo (como reinicialização, redefinição de fábrica e atualização de firmware) de um aplicativo de back-end na nuvem. O dispositivo é responsável por:

* Tratamento da solicitação de método enviada do Hub IoT.
* Iniciando a ação específica do dispositivo correspondente no dispositivo.
* Fornecer atualizações de status por meio de *Propriedades* relatadas ao Hub IOT.

Você pode usar um aplicativo de back-end na nuvem para executar consultas de dispositivo de cópia para relatar o progresso de suas ações de gerenciamento de dispositivo.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
