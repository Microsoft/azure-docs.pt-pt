---
title: Guia de desenvolvimento de dispositivos (C) - IoT Plug and Play | Microsoft Docs
description: Descrição de IoT Plug and Play para desenvolvedores de dispositivos C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 0cca47269e632e1fcba1f8f9eb1c835f27e63059
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582840"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Guia de desenvolvimento de dispositivos IoT Plug e Play

O IoT Plug and Play permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

Um dispositivo inteligente pode ser implementado diretamente, usar [módulos,](../iot-hub/iot-hub-devguide-module-twins.md)ou usar [módulos IoT Edge](../iot-edge/about-iot-edge.md).

Este guia descreve os passos básicos necessários para criar um módulo de dispositivo, módulo ou IoT Edge que segue as [convenções IoT Plug and Play](../iot-pnp/concepts-convention.md).

Para construir um dispositivo IoT Plug and Play, módulo ou módulo IoT Edge, siga estes passos:

1. Certifique-se de que o seu dispositivo está a utilizar o protocolo MQTT ou MQTT sobre WebSockets para ligar ao Azure IoT Hub.
1. Crie um modelo [digital de linguagem de definição de gémeos (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever o seu dispositivo. Para saber mais, consulte [os componentes nos modelos IoT Plug and Play](concepts-modeling-guide.md).
1. Atualize o seu dispositivo ou módulo para anunciar o `model-id` como parte da ligação do dispositivo.
1. Implementar telemetria, propriedades e comandos usando as [convenções IoT Plug and Play](concepts-convention.md)

Assim que a implementação do dispositivo ou do módulo estiver pronto, utilize o [explorador Azure IoT](howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções IoT Plug and Play.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre o desenvolvimento de dispositivos IoT Plug e Play, aqui estão alguns recursos adicionais:

- [Linguagem Digital Twins Definition (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [Compreender componentes nos modelos IoT Plug and Play](concepts-modeling-guide.md)
- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)
- [Guia de desenvolvedores de serviço ioT plug e play](concepts-developer-guide-service.md)