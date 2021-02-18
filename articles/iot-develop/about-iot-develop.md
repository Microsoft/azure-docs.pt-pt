---
title: Introdução ao dispositivo Azure IoT e desenvolvimento de aplicações
description: Aprenda a usar o Azure IoT para fazer o desenvolvimento de dispositivos incorporados e construir aplicações em nuvem ativadas por dispositivos.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655027"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>O que é o dispositivo Azure IoT e o desenvolvimento de aplicações?

O Azure IoT é uma coleção de serviços geridos e de plataforma que ligam, monitorizam e controlam os seus dispositivos IoT. O Azure IoT oferece aos desenvolvedores um conjunto abrangente de opções. As suas opções incluem plataformas de dispositivos, serviços de suporte à nuvem, SDKs e ferramentas para a construção de aplicações em nuvem ativadas por dispositivos.

Este artigo reveste várias considerações fundamentais para os desenvolvedores que estão começando com Azure IoT. Estes conceitos irão orientá-lo, como um desenvolvedor de dispositivos IoT, para as suas opções de IoT Azure e como começar. Especificamente, o artigo geralmente estes conceitos:
- [Compreender as funções de desenvolvimento de dispositivos](#device-development-roles)
- [Escolher o seu hardware](#choosing-your-hardware)
- [Escolher um SDK](#choosing-an-sdk)
- [Selecionando opções de ligação](#selecting-connection-options)

## <a name="device-development-roles"></a>Funções de desenvolvimento de dispositivos
Este artigo discute dois papéis comuns que pode observar entre os desenvolvedores de dispositivos. Como usado aqui, um papel é uma coleção de tarefas de desenvolvimento relacionadas. É útil entender em que tipo de desenvolvimento está a trabalhar. O seu papel tem impacto em muitas escolhas de desenvolvimento que faz.

* **Desenvolvimento da aplicação do dispositivo:** Alinha-se com práticas de desenvolvimento modernas, visa muitas das línguas de ordem superior, e executa num sistema operativo de uso geral, como Windows ou Linux.

* **Desenvolvimento de dispositivos incorporados:** Descreve o desenvolvimento direcionando dispositivos constrangidos de recursos. Um dispositivo limitado por recursos será frequentemente utilizado para reduzir os custos unitários, o consumo de energia ou o tamanho do dispositivo. Estes dispositivos têm controlo direto sobre a plataforma de hardware em que executam.

### <a name="device-application-development"></a>Desenvolvimento de aplicações de dispositivos
Os desenvolvedores de aplicações de dispositivos estão a adaptar os dispositivos existentes para se conectarem à nuvem e integrarem-se nas suas soluções IoT. Estes dispositivos podem suportar idiomas de ordem superior, como C# ou Python, e muitas vezes suportam um sistema operativo de finalidade geral robusto, como Windows ou Linux. Os dispositivos-alvo comuns incluem computadores, contentores, Pis de framboesa e dispositivos móveis. 

Em vez de desenvolverem dispositivos constrangidos em escala, estes desenvolvedores focam-se em permitir um cenário de IoT específico exigido pela sua solução em nuvem. Alguns destes desenvolvedores também trabalharão em dispositivos limitados para a sua solução em nuvem. Para desenvolvedores que trabalham com dispositivos constrangidos, consulte o caminho [de Desenvolvimento de Dispositivos Incorporados](#embedded-device-development) abaixo.

> [!TIP]
> Consulte os [SDKs de dispositivos não constrangidos](about-iot-sdks.md#unconstrained-device-sdks) para começar.

### <a name="embedded-device-development"></a>Desenvolvimento de dispositivos embutidos
Os alvos de desenvolvimento incorporados constituem dispositivos limitados que têm memória e processamento limitados. Os dispositivos constrangidos restringem o que pode ser alcançado em comparação com uma plataforma de desenvolvimento tradicional.

Os dispositivos incorporados normalmente utilizam um sistema operativo em tempo real (RTOS) ou nenhum sistema operativo. Os dispositivos incorporados têm controlo total sobre o seu hardware, devido à falta de um sistema operativo de finalidade geral. Este facto faz dos dispositivos incorporados uma boa escolha para sistemas em tempo real.

Os Atuais SDKs incorporados visam a língua **C.** Os SDKs incorporados não fornecem nenhum sistema operativo ou suporte Azure RTOS. São projetados com alvos embutidos em mente. As considerações de design incluem a necessidade de uma pegada mínima, e um design de atribuição de memórias.

Se o seu dispositivo for capaz de executar um sistema operativo de uso geral, recomendamos seguir o caminho [de Desenvolvimento da Aplicação do Dispositivo.](#device-application-development) Oferece um conjunto mais rico de opções de desenvolvimento.

> [!TIP]
> Consulte os [SDKs de dispositivo constrangido](about-iot-sdks.md#constrained-device-sdks) para começar.

## <a name="choosing-your-hardware"></a>Escolher o seu hardware
Os dispositivos Azure IoT são os blocos básicos de construção de uma solução IoT e são responsáveis por observar e interagir com o seu ambiente. Existem muitos tipos diferentes de dispositivos IoT, e é útil entender os tipos de dispositivos que existem e como estes podem afetar o seu processo de desenvolvimento.

Para obter mais informações sobre a diferença entre os tipos de dispositivos abrangidos neste artigo, leia [sobre os tipos de dispositivos IoT](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Escolher um SDK
Como desenvolvedor de dispositivos Azure IoT, você tem um conjunto diversificado de SDKs de dispositivo, e SDKs de serviço Azure, para ajudá-lo a construir aplicações em nuvem ativadas por dispositivos. Os SDKs irão simplificar o seu esforço de desenvolvimento e simplificar grande parte da complexidade da ligação e gestão de dispositivos. 

Como indicado na secção de [funções de desenvolvimento](#device-development-roles) do dispositivo, existem três tipos de SDKs IoT para o desenvolvimento do dispositivo:
- SDKs de dispositivo incorporado (para dispositivos constrangidos)
- SDKs do dispositivo (para utilizar idiomas de ordem superior para ligar dispositivos existentes a aplicações IoT)
- SDKs de serviço (para a construção de soluções Azure IoT que ligam dispositivos a serviços)

Para saber mais sobre a escolha de um dispositivo Azure IoT ou serviço SDK, consulte [a visão geral dos SDKs de dispositivoS Azure IoT](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Selecionando opções de ligação
Um passo importante no processo de desenvolvimento é escolher o conjunto de opções que utilizará para ligar e gerir os seus dispositivos. Há dois aspetos críticos a considerar:
- Escolhendo uma plataforma de aplicação IoT para hospedar os seus dispositivos. Para Azure IoT, isto significa escolher o IoT Hub ou o IoT Central.
- Escolher ferramentas de desenvolvimento para ajudá-lo a conectar, gerir e monitorizar dispositivos.

Para saber mais sobre a seleção de uma plataforma e ferramentas de aplicação, consulte [o Resumo: Opções de conexão para os desenvolvedores de dispositivos Azure IoT](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Passos seguintes
Selecione uma das seguintes séries de arranque rápido que é mais relevante para o seu papel de desenvolvimento. Estes artigos demonstram o básico da criação de uma aplicação Azure IoT para dispositivos de hospedagem, utilizando um SDK, ligando um dispositivo e enviando telemetria.  
- Para o desenvolvimento da aplicação do dispositivo:  [Quickstart: Enviar telemetria de um dispositivo para Azure IoT Central](quickstart-send-telemetry-python.md)
- Para o desenvolvimento de dispositivos [incorporados: Começar com o desenvolvimento de dispositivos incorporados Azure IoT](quickstart-device-development.md)
