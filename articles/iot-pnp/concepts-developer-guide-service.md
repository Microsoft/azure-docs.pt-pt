---
title: Guia de desenvolvedores de serviços - IoT Plug and Play | Microsoft Docs
description: Descrição de IoT Plug and Play para desenvolvedores de serviços
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582736"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia de desenvolvedores de serviço ioT plug e play

O IoT Plug and Play permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

O IoT Plug and Play permite-lhe utilizar dispositivos que anunciaram o seu ID do modelo com o seu hub IoT. Por exemplo, pode aceder diretamente às propriedades e comandos de um dispositivo.

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, um dos SDKs de serviço IoT:

## <a name="service-sdks"></a>SDKs de Serviço

Utilize os SDKs de serviço Azure IoT na sua solução para interagir com dispositivos e módulos. Por exemplo, pode utilizar os SDKs de serviço para ler e atualizar propriedades gémeas e invocar comandos. As línguas apoiadas incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem-lhe aceder a informações do dispositivo a partir de uma solução, como um ambiente de trabalho ou uma aplicação web. Os SDKs de serviço incluem dois espaços de nome e modelos de objetos que pode usar para recuperar o ID do modelo:

- Cliente de serviço Iot Hub. Este serviço expõe o ID do modelo como uma propriedade gémea do dispositivo.

- Cliente digital das Gémeas Marotas. A nova API digital twins opera no modelo [Digital Twins Definition Language (DTDL)](concepts-digital-twin.md) que constrói componentes, propriedades e comandos. As APIs Digitais Twin facilitam aos construtores de soluções a criação de soluções IoT Plug e Play.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem Digital Twins Definition (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [Guia de modelagem IoT Plug and Play](concepts-modeling-guide.md)
- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)