---
title: IoT Plug e Play bibliotecas e SDKs
description: Informações sobre o dispositivo e bibliotecas de serviçodisponíveis para o desenvolvimento de soluções habilitadas para plug e reprodução ioT.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064336"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug e Play bibliotecas e SDKs

As bibliotecas IoT Plug and Play e SDKs permitem aos desenvolvedores construir soluções IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela seguinte inclui links para amostras e quickstarts para ajudá-lo a começar:

## <a name="microsoft-supported-libraries-and-sdks"></a>Bibliotecas e SDKs suportados pela Microsoft

| Plataforma | Biblioteca/Pacote | Código de origem | Sample | Início Rápido | Referência |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Dispositivo SDK no apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Ligar a um Hub IoT](./quickstart-connect-pnp-device-c-linux.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Dispositivo SDK em Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Ligar a um Hub IoT](./quickstart-connect-pnp-device-c-windows.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [SDK do dispositivo em EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Dispositivo SDK em Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Dispositivo SDK no CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Amostras de Twin Digital](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Ligar a um Hub IoT](./quickstart-connect-pnp-device-csharp.md) | [Referência](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Amostras de Twin Digital](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Ligar a um Hub IoT](./quickstart-connect-pnp-device-java.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Amostras de Twin Digital](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Ligar a um Hub IoT](./quickstart-connect-pnp-device-node.md) | [Referência](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Suporte do Hub IoT

As capacidades do dispositivo IoT Plug and Play são suportadas apenas por [centros IoT de nível gratuito e padrão.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>Passos seguintes

Além do dispositivo SDKs e bibliotecas, pode utilizar APIs REST para interagir com os repositórios do modelo.