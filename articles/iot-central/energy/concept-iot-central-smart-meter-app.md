---
title: Conceitos arquitetónicos em Azure IoT Central - Energia Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à arquitetura do modelo de aplicações de energia Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024318"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - arquitetura de aplicativos de contadores inteligentes



Este artigo fornece uma visão geral da arquitetura do modelo de modelo de monitorização de contadores inteligentes. O diagrama abaixo mostra uma arquitetura comumente usada para app de contadores inteligentes em Azure usando a plataforma IoT Central.

> [!div class="mx-imgBorder"]
> ![arquitetura de contador inteligente](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas soluções podem não requerer todos os componentes listados aqui.

## <a name="smart-meters-and-connectivity"></a>Contadores inteligentes e conectividade 

Um contador inteligente é um dos dispositivos mais importantes entre todos os ativos energéticos. Regista e comunica dados sobre consumo de energia a serviços públicos para monitorização e outros casos de utilização, tais como a faturação e a resposta à procura. Com base no tipo de contador, pode ligar-se à IoT Central utilizando gateways ou outros dispositivos ou sistemas intermédios, tais dispositivos de borda e sistemas de extremidade da cabeça. Construa a ponte do dispositivo Central IoT para ligar dispositivos, que não podem ser ligados diretamente. A ponte do dispositivo IoT Central é uma solução de código aberto e você pode encontrar os detalhes completos [aqui](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 


## <a name="iot-central-platform"></a>Plataforma Central IoT

A Azure IoT Central é uma plataforma que simplifica a construção da sua solução IoT e ajuda a reduzir os encargos e custos da gestão, operações e desenvolvimento de IoT. Com a IoT Central, pode facilmente conectar, monitorizar e gerir os seus ativos de Internet das Coisas (IoT) em escala. Depois de ligar os seus contadores inteligentes à IoT Central, o modelo de aplicação utiliza funcionalidades incorporadas, tais como modelos de dispositivos, comandos e dashboards. O modelo de aplicação também utiliza o armazenamento IoT Central para cenários de caminhos quentes, tais como monitorização de dados, análise, regras e visualização de contadores em tempo real. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de exibilidade para construir com a IoT Central
A plataforma IoT Central fornece duas opções de extebilidade: Exportação Contínua de Dados (CDE) e APIs. Os clientes e parceiros podem escolher entre estas opções com base em personalizar as suas soluções para necessidades específicas. Por exemplo, um dos nossos parceiros configurou o CDE com o Armazenamento do Lago De Dados Azure (ADLS). Estão a usar ADLS para retenção de dados a longo prazo e outros cenários de armazenamento de caminhos frios, tais como o processamento de lotes, auditoria e comunicação. 

## <a name="next-steps"></a>Passos seguintes

* Agora que aprendeu sobre a arquitetura, [crie uma aplicação de contadores inteligentes gratuitamente](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Para saber mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/)
