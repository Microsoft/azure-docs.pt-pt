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
ms.openlocfilehash: 31e06777a2f2e26f6ef546e60fd0bf4428d272c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503815"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDKs para IoT Plug e Play

As bibliotecas IoT Plug and Play e SDKs permitem aos desenvolvedores construir soluções IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela seguinte inclui links para amostras e quickstarts para ajudá-lo a começar:

## <a name="device-sdks"></a>SDKs de dispositivo

| Linguagem | Pacote | Repositório de Código | Amostras | Início Rápido | Referência |
|---|---|---|---|---|---|
| C - Dispositivo | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device.md) | [Referência](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - Dispositivo | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Ligar a um Hub IoT](quickstart-connect-device.md) | [Referência](/dotnet/api/microsoft.azure.devices.client) |
| Java - Dispositivo | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ligar a um Hub IoT](quickstart-connect-device.md) | [Referência](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python - Dispositivo | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device.md) | [Referência](/python/api/azure-iot-device/azure.iot.device) |
| Nó - Dispositivo | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ligar a um Hub IoT](quickstart-connect-device.md) | [Referência](/javascript/api/azure-iot-device/) |
| Incorporado C - Dispositivo | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Amostras](howto-use-embedded-c.md#samples) | [Como usar O C Incorporado](howto-use-embedded-c.md) | N/D

## <a name="service-sdks"></a>SDKs de Serviço

| Plataforma  | Pacote | Repositório de Código | Amostras | Início Rápido | Referência |
|---|---|---|---|---|---|
| .NET - Serviço IoT Hub | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Amostras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/D | [Referência](/dotnet/api/microsoft.azure.devices) |
| Serviço Java - IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/D | [Referência](/java/api/com.microsoft.azure.sdk.iot.service) |
| Nó - Serviço IoT Hub | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/D | [Referência](/javascript/api/azure-iothub/) |
| Python - Serviço Digital Twins | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interaja com ioT Hub Digital Twins API](quickstart-service.md) | N/D |
| Nó - Serviço Digital Twins | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interaja com ioT Hub Digital Twins API](quickstart-service.md) | N/D |

## <a name="next-steps"></a>Passos seguintes

Para experimentar os SDKs e bibliotecas, consulte o  [Guia do Programador](concepts-developer-guide-device.md) e o [dispositivo acelera](quickstart-connect-device.md) e inicia [rapidamente o serviço](quickstart-service.md).