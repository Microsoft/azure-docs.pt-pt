---
title: Conceitos arquitetônicos em Azure IoT Central - | de contadores inteligentes Microsoft Docs
description: Este artigo introduz conceitos-chave relacionados com a arquitetura do modelo de aplicação de energia Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 48dd95acf725e57d63f85c54c97b641935b049b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832373"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - arquitetura de aplicativos de contadores inteligentes

Este artigo fornece uma visão geral da arquitetura do modelo de aplicação de monitorização de contadores inteligentes. O diagrama abaixo mostra uma arquitetura comumente usada para aplicação de contadores inteligentes em Azure usando a plataforma IoT Central.

> [!div class="mx-imgBorder"]
> ![arquitetura de contadores inteligentes](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas soluções podem não exigir todos os componentes listados aqui.

## <a name="smart-meters-and-connectivity"></a>Contadores inteligentes e conectividade 

Um contador inteligente é um dos dispositivos mais importantes entre todos os ativos energéticos. Regista e comunica dados de consumo de energia a utilitários para monitorização e outros casos de utilização, como a faturação e a resposta à procura. Com base no tipo de contador, pode ligar-se à IoT Central utilizando gateways ou outros dispositivos ou sistemas intermédios, tais dispositivos de borda e sistemas de extremidade da cabeça. Construa a ponte do dispositivo IoT Central para ligar dispositivos, que não podem ser ligados diretamente. A ponte de dispositivos IoT Central é uma solução de código aberto e pode encontrar os detalhes completos [aqui.](../core/howto-build-iotc-device-bridge.md) 

## <a name="iot-central-platform"></a>Plataforma Central IoT

A Azure IoT Central é uma plataforma que simplifica a construção da sua solução IoT e ajuda a reduzir os encargos e custos da gestão, operações e desenvolvimento de IoT. Com a IoT Central, pode facilmente conectar, monitorizar e gerir os seus ativos de Internet das Coisas (IoT) em escala. Depois de ligar os seus contadores inteligentes ao IoT Central, o modelo de aplicação utiliza funcionalidades incorporadas, tais como modelos de dispositivos, comandos e dashboards. O modelo de aplicação também usa o armazenamento IoT Central para cenários de caminhos quentes, tais como monitorização de dados de contadores em tempo real, análise, regras e visualização. 

## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de extensibilidade para construir com a IoT Central
A plataforma IoT Central oferece duas opções de extensibilidade: Exportação contínua de dados (CDE) e APIs. Os clientes e parceiros podem escolher entre estas opções baseadas em personalizar as suas soluções para necessidades específicas. Por exemplo, um dos nossos parceiros configurava o CDE com o Azure Data Lake Storage (ADLS). Estão a usar ODLS para retenção de dados a longo prazo e outros cenários de armazenamento de caminhos frios, tais fins de processamento de lotes, auditoria e reporte. 

## <a name="next-steps"></a>Passos seguintes

* Agora que aprendeu sobre a arquitetura, [crie gratuitamente a app de contadores inteligentes](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Para saber mais sobre o IoT Central, consulte [a visão geral do IoT Central](../index.yml)
