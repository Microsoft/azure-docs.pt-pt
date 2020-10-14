---
title: IoT Plug e Play lançamento atual / Microsoft Docs
description: Saiba o que está incluído no atual lançamento IoT Plug e Play.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5b682e371a341c8dbd707652acc60773663326dc
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019125"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>O que está no atual lançamento ioT plug e play

Este artigo resume as ferramentas, SDKs e APIs que suportam a atual versão IoT Plug and Play. Os números de versão mostrados refletem o número da versão no momento em que o IoT Plug e o Play ficaram geralmente disponíveis. Os números da versão podem aumentar após o lançamento.

## <a name="modeling-language"></a>Linguagem de modelação

[Linguagem de definição de gémeos digitais (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Para saber mais sobre como os dispositivos IoT Plug e Play funcionam com o DTDL, consulte [as convenções IoT Plug and Play](concepts-convention.md).

## <a name="tools-and-utilities"></a>Ferramentas e utilitários

- Explorador Azure IoT 0.12.0.

    Para saber mais, consulte [instalar e utilizar o explorador Azure IoT](howto-use-iot-explorer.md).

- Extensão vs código 1.0.0.

    Para saber mais, consulte [instalar e utilizar as ferramentas de autoria DTDL.](howto-use-dtdl-authoring-tools.md)

- Extensão 1.0.0 do Visual Studio 2019.

    Para saber mais, consulte [instalar e utilizar as ferramentas de autoria DTDL.](howto-use-dtdl-authoring-tools.md)

- Extensão Azure CLI IoT 0.10.0.

    A extensão Azure IoT inclui comandos para ajudar a certificar dispositivos. Consulte `az iot product -h`.

## <a name="libraries-and-sdks"></a>Bibliotecas e SDKs

Para saber mais sobre as bibliotecas e SDKs, consulte [microsoft SDKs para IoT Plug e Play](libraries-sdks.md).

- Dispositivo C SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Dispositivo C incorporado SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET dispositivo SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Dispositivo Java SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Dispositivo python SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js dispositivo SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - Serviço IoT Hub [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - Serviço IoT Hub [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT Hub serviço [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT Hub/Digital Twins serviço [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- Parser de modelo DTDL [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>APIs REST

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Para saber mais, consulte [o guia de desenvolvimento IoT Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>Hub IoT

IoT Plug and Play é suportado pelo IoT Hub em todas as regiões. IoT Plug and Play só é suportado por hubs IoT standard ou free tier.

## <a name="announcements"></a>Anúncios

Para anúncios atuais e anteriores do IoT Plug and Play, consulte as seguintes publicações de blog:

- [Atualização de pré-visualização pública (Posted on August 29, 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Prepare e certifique os seus dispositivos para IoT Plug and Play (Publicado em 26 de agosto de 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Plug and Play já está disponível em pré-visualização (Publicado a 22 de agosto de 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Construa com Azure IoT Central e IoT Plug and Play (Posted on May 7, 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)
