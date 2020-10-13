---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580067"
---
O IoT Plug and Play permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

Um dispositivo inteligente pode ser implementado diretamente, usar [módulos,](../articles/iot-hub/iot-hub-devguide-module-twins.md)ou usar [módulos IoT Edge](../articles/iot-edge/about-iot-edge.md).

Este guia descreve os passos básicos necessários para criar um módulo de dispositivo, módulo ou IoT Edge que segue as [convenções IoT Plug and Play](../articles/iot-pnp/concepts-convention.md).

Para construir um dispositivo IoT Plug and Play, módulo ou módulo IoT Edge, siga estes passos:

1. Certifique-se de que o seu dispositivo está a utilizar o protocolo MQTT ou MQTT sobre WebSockets para ligar ao Azure IoT Hub.
1. Crie um modelo [digital de linguagem de definição de gémeos (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever o seu dispositivo. Para saber mais, consulte [os componentes nos modelos IoT Plug and Play](../articles/iot-pnp/concepts-components.md).
1. Atualize o seu dispositivo ou módulo para anunciar o `model-id` como parte da ligação do dispositivo.
1. Implementar telemetria, propriedades e comandos usando as [convenções IoT Plug and Play](../articles/iot-pnp/concepts-convention.md)

Assim que a implementação do dispositivo ou do módulo estiver pronto, utilize o [explorador Azure IoT](../articles/iot-pnp/howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções IoT Plug and Play.
