---
title: IoT Plug e Play lançamento atual / Microsoft Docs
description: Saiba o que está incluído no atual lançamento IoT Plug e Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583565"
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

    Para saber mais, consulte [instalar e utilizar a extensão Azure IoT para o Azure CLI](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > A extensão Azure IoT inclui comandos para ajudar a certificar dispositivos. Consulte `az iot product -h`.



## <a name="libraries-and-sdks"></a>Bibliotecas e SDKs

Para saber mais sobre as bibliotecas e SDKs, consulte [microsoft SDKs para IoT Plug e Play](libraries-sdks.md).

- Dispositivo C SDK [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- .NET device SDK [NuGet Microsoft.Azure.Devices.Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Dispositivo Java SDK [Maven iot-device-cliente 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Dispositivo python SDK [Pip azure-iot-dispositivo v2.2.0](https://pypi.org/project/azure-iot-device/)
- Node.js dispositivo SDK [npm azure-iot-dispositivo 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET - Serviço IoT Hub [NuGet Microsoft.Azure.Dispositivos 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java - IoT Hub serviço [Maven iot-service-cliente 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js - IoT Hub service [npm azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python - Serviço IoT Hub/Digital Twins [Pip azure-iot-hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- Parser de modelo DTDL [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

### <a name="preview"></a>Pré-visualizar

- Azure SDK para Incorporado [1.0.0-preview.5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Azure RTOS IoT [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

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

