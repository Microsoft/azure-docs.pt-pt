---
title: IoT Plug e Play bibliotecas e SDKs
description: Informações sobre o dispositivo e bibliotecas de serviços disponíveis para o desenvolvimento de soluções IoT Plug e Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407800"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDKs para IoT Plug e Play

As bibliotecas IoT Plug and Play e SDKs permitem aos desenvolvedores construir soluções IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela seguinte inclui links para amostras e quickstarts para ajudá-lo a começar:

## <a name="device-sdks-ga"></a>SDKs de dispositivo (GA)

| Idioma | Pacote | Repositório de Código | Amostras | Início Rápido | Referência |
|---|---|---|---|---|---|
| C - Dispositivo | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device-c.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Dispositivo | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Ligar a um Hub IoT](quickstart-connect-device-csharp.md) | [Referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java - Dispositivo | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ligar a um Hub IoT](quickstart-connect-device-java.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python - Dispositivo | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device-python.md) | [Referência](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Nó - Dispositivo | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device-node.md) | [Referência](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>SDKs do dispositivo (pré-visualização)

| Idioma | Repositório de Código/Amostras |
|---|---|
|Azure SDK para Incorporado| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Middleware Azure RTOS IoT| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTOS começando guias | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>SDKs de serviço (pré-visualização)

| Idioma | Pacote | Repositório de Código | Amostras | Início Rápido | Referência |
|---|---|---|---|---|---|
| .NET - Pré-visualização do serviço IoT Hub | [NuGet 1.27.1-pré-visualização-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | N/D | N/D |
| Pré-visualização do serviço IoT Hub | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | N/D | N/D |
| Nó - Pré-visualização do serviço IoT Hub | [npm 1.12.4-pnp-refresh.4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | N/D | N/D |
| Pré-visualização do serviço Python - IoT Hub/Digital Twins | [pip 2.2.1rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interaja com ioT Hub Digital Twins API](quickstart-service-python.md) | N/D |
| Nó - Pré-visualização do serviço Digital Twins | [npm 1.0.0-pnp-refresh.3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interaja com ioT Hub Digital Twins API](quickstart-service-node.md) | N/D |

## <a name="next-steps"></a>Próximos passos

Para experimentar os SDKs e bibliotecas, consulte o [Guia do Programador](concepts-developer-guide.md) e o [dispositivo acelera](quickstart-connect-device-c.md) e inicia [rapidamente o serviço](quickstart-service-node.md).
