---
title: Conceitos arquitetônicos em Azure IoT Central - painel solar Microsoft Docs
description: Este artigo introduz conceitos-chave relacionados com a arquitetura da app de monitorização do painel solar Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 00ef6cf6d4149f139876cb0c2d845133ba00157c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127511"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - arquitetura de aplicativos de painel solar




Este artigo fornece uma visão geral da arquitetura do modelo de aplicativo de monitorização do painel solar. O diagrama abaixo mostra uma arquitetura comumente usada para aplicação de painel solar em Azure usando a plataforma IoT Central.

> [!div class="mx-imgBorder"]
> ![arquitetura de contadores inteligentes](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas aplicações podem não requerer todos os componentes listados aqui.

## <a name="solar-panels-and-connectivity"></a>Painéis solares e conectividade 

Os painéis solares são uma das fontes significativas de energia renovável. Dependendo do tipo de painel solar e configurado, pode conectá-lo utilizando gateways ou outros dispositivos intermédios e sistemas proprietários. Pode ser necessário construir uma ponte de dispositivos IoT Central para ligar dispositivos, que não podem ser ligados diretamente. A ponte de dispositivos IoT Central é uma solução de código aberto e pode encontrar os detalhes completos [aqui.](../core/howto-build-iotc-device-bridge.md) 



## <a name="iot-central-platform"></a>Plataforma Central IoT
A Azure IoT Central é uma plataforma que simplifica a construção da sua solução IoT e ajuda a reduzir os encargos e custos da gestão, operações e desenvolvimento de IoT. Com a IoT Central, pode facilmente conectar, monitorizar e gerir os seus ativos de Internet das Coisas (IoT) em escala. Depois de ligar os seus painéis solares ao IoT Central, o modelo de aplicação utiliza funcionalidades incorporadas, tais como modelos de dispositivos, comandos e dashboards. O modelo de aplicação também usa o armazenamento IoT Central para cenários de caminhos quentes, tais como monitorização de dados de contadores em tempo real, análise, regras e visualização.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de extensibilidade para construir com a IoT Central
A plataforma IoT Central oferece duas opções de extensibilidade: Exportação contínua de dados (CDE) e APIs. Os clientes e parceiros podem escolher entre estas opções baseadas em personalizar as suas soluções para necessidades específicas. Por exemplo, um dos nossos parceiros configurava o CDE com o Azure Data Lake Storage (ADLS). Estão a usar ODLS para retenção de dados a longo prazo e outros cenários de armazenamento de caminhos frios, tais fins de processamento, auditoria e reporte. 

## <a name="next-steps"></a>Passos seguintes

* Agora que aprendeu sobre a arquitetura, [crie uma app de painel solar gratuitamente](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Para saber mais sobre o IoT Central, consulte [a visão geral do IoT Central](../index.yml)