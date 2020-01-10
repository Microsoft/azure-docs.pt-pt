---
title: SDKs e bibliotecas de Plug and Play de IoT
description: Informações sobre as bibliotecas de dispositivo e serviço disponíveis para o desenvolvimento de soluções habilitadas para IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b328d7fe4cf3a3487614ed93dcf130aa7a233efd
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830574"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>SDKs e bibliotecas de Plug and Play de IoT

As bibliotecas e SDKs do IoT Plug and Play permitem que os desenvolvedores criem soluções de IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela a seguir inclui links para exemplos e guias de início rápido para ajudá-lo a começar:

## <a name="microsoft-supported-libraries-and-sdks"></a>Bibliotecas e SDKs com suporte da Microsoft

| Plataforma | Biblioteca/pacote | Código fonte | Exemplo | Início Rápido | Referência |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [SDK do dispositivo em apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemplos de cliente de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Conectar-se ao Hub IoT](./quickstart-connect-pnp-device-c-linux.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [SDK do dispositivo em Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemplos de cliente de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Conectar-se ao Hub IoT](./quickstart-connect-pnp-device-c-windows.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Mbed  | [SDK do dispositivo no EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemplos de cliente de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [SDK do dispositivo no IDE do Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemplos de cliente de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [SDK do dispositivo em CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Exemplos de cliente de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Amostras de entrelaçamento digital](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Conectar-se ao Hub IoT](./quickstart-connect-pnp-device-csharp.md) | [Referência](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Amostras de entrelaçamento digital](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Conectar-se ao Hub IoT](./quickstart-connect-pnp-device-java.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Amostras de entrelaçamento digital](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Conectar-se ao Hub IoT](./quickstart-connect-pnp-device-node.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |

## <a name="iot-hub-support"></a>Suporte ao Hub IoT

Os recursos de dispositivo de Plug and Play IoT só têm suporte de [hubs IOT de camada Standard e gratuita](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Passos seguintes

Além dos SDKs e bibliotecas do dispositivo, você pode usar APIs REST para interagir com os repositórios de modelo.