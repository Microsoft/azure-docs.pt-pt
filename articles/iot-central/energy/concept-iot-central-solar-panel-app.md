---
title: Conceitos arquitetónicos em Azure IoT Central - Energia Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à arquitetura da Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77018011"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - arquitetura de aplicativos de painel solar




Este artigo fornece uma visão geral da arquitetura do modelo de aplicação de monitorização do painel solar. O diagrama abaixo mostra uma arquitetura comumente usada para app de painéis solares em Azure usando a plataforma IoT Central.

> [!div class="mx-imgBorder"]
> ![arquitetura de contador inteligente](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas aplicações podem não requerer todos os componentes listados aqui.

## <a name="solar-panels-and-connectivity"></a>Painéis solares e conectividade 

Os painéis solares são uma das fontes significativas de energia renovável. Dependendo do tipo de painel solar e configuração, pode ligá-lo utilizando gateways ou outros dispositivos intermédios e sistemas proprietários. Pode ser necessário construir uma ponte de dispositivos IoT Central para ligar dispositivos, que não podem ser ligados diretamente. A ponte do dispositivo IoT Central é uma solução de código aberto e você pode encontrar os detalhes completos [aqui](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 



## <a name="iot-central-platform"></a>Plataforma Central IoT
A Azure IoT Central é uma plataforma que simplifica a construção da sua solução IoT e ajuda a reduzir os encargos e custos da gestão, operações e desenvolvimento de IoT. Com a IoT Central, pode facilmente conectar, monitorizar e gerir os seus ativos de Internet das Coisas (IoT) em escala. Depois de ligar os seus painéis solares à IoT Central, o modelo de aplicação utiliza funcionalidades incorporadas, tais como modelos de dispositivos, comandos e dashboards. O modelo de aplicação também utiliza o armazenamento IoT Central para cenários de caminhos quentes, tais como monitorização de dados, análise, regras e visualização de contadores em tempo real.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de exibilidade para construir com a IoT Central
A plataforma IoT Central fornece duas opções de extebilidade: Exportação Contínua de Dados (CDE) e APIs. Os clientes e parceiros podem escolher entre estas opções com base em personalizar as suas soluções para necessidades específicas. Por exemplo, um dos nossos parceiros configurou o CDE com o Armazenamento do Lago De Dados Azure (ADLS). Estão a usar ADLS para retenção de dados a longo prazo e outros cenários de armazenamento de caminhos frios, tais como processamento de lotes, auditoria e finalidades de reporte. 

## <a name="next-steps"></a>Passos seguintes

* Agora que aprendeu sobre a arquitetura, [crie uma aplicação](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) de painel solar gratuitamente
* Para saber mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/)