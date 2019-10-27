---
title: Conceitos de arquitetura no Azure IoT Central-Energy | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à arquitetura do Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 61a2b00bee7b16e3cc62a010ca454466cb0292a0
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965572"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central-arquitetura de aplicativo de painel solar

Este artigo fornece uma visão geral da arquitetura do modelo de aplicativo de monitoramento do painel solar. O diagrama a seguir mostra uma arquitetura comumente usada para o aplicativo de painel solar no Azure usando IoT Central plataforma.

> [!div class="mx-imgBorder"]
> ![arquitetura de medidor inteligente](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas aplicações podem não requerer todos os componentes listados aqui.

## <a name="solar-panels-and-connectivity"></a>Conectividade e painéis solares 

Os painéis solares são uma das fontes significativas de energia renovável. Dependendo do tipo de painel solar e da configuração, você pode conectá-lo por meio de gateways ou por outros dispositivos intermediários e sistemas proprietários. Talvez seja necessário criar IoT Central ponte de dispositivo para conectar dispositivos, que não podem ser conectados diretamente. A ponte de dispositivo IoT Central é uma solução de software livre e você pode encontrar os detalhes completos [aqui](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 



## <a name="iot-central-platform"></a>Plataforma IoT Central
O Azure IoT Central é uma plataforma que simplifica a criação de sua solução de IoT e ajuda a reduzir a carga e os custos de gerenciamento, operações e desenvolvimento de IoT. Com o IoT Central, você pode facilmente conectar, monitorar e gerenciar seus ativos de Internet das Coisas (IoT) em escala. Depois de conectar os painéis solar ao IoT Central, o modelo de aplicativo usa recursos internos, como modelos de dispositivo, comandos e painéis. O modelo de aplicativo também usa o armazenamento de IoT Central para cenários de caminho quente, como monitoramento de dados de medidor, análise, regras e visualização quase em tempo real.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de extensibilidade para criar com IoT Central
A plataforma IoT Central fornece duas opções de extensibilidade: CDE (exportação de dados contínuas) e APIs. Os clientes e parceiros podem escolher entre essas opções com base para personalizar suas soluções para necessidades específicas. Por exemplo, um de nossos parceiros configuraram o CDE com Azure Data Lake Storage (ADLS). Eles estão usando ADLS para retenção de dados de longo prazo e outros cenários de armazenamento de caminho frio, tais processos de processamento em lote, auditoria e relatórios. 

## <a name="next-steps"></a>Passos seguintes

* Agora que você aprendeu sobre a arquitetura, [crie um aplicativo de painel solar gratuitamente](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Para saber mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/)