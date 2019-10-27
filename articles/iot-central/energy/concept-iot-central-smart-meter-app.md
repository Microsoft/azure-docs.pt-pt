---
title: Conceitos de arquitetura no Azure IoT Central-Energy | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à arquitetura do modelo de aplicativo de energia do Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: f87e1a7893fa8c2ea22711f90696a5d2836eb3ac
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965615"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – arquitetura de aplicativo de medidor inteligente

Este artigo fornece uma visão geral da arquitetura de modelo do aplicativo de monitoramento do medidor inteligente. O diagrama a seguir mostra uma arquitetura comumente usada para o aplicativo de medidor inteligente no Azure usando IoT Central plataforma.

> [!div class="mx-imgBorder"]
> ![arquitetura de medidor inteligente](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Esta arquitetura é composta pelos seguintes componentes. Algumas soluções podem não exigir todos os componentes listados aqui.

## <a name="smart-meters-and-connectivity"></a>Medidores inteligentes e conectividade 

Um medidor inteligente é um dos dispositivos mais importantes entre todos os ativos de energia. Ele registra e comunica dados de consumo de energia para utilitários para monitoramento e outros casos de uso, como cobrança e resposta de demanda. Com base no tipo de medidor, ele pode se conectar a IoT Central por meio de gateways ou por meio de outros dispositivos ou sistemas intermediários, como dispositivos de borda e sistemas de cabeça-end. Crie IoT Central ponte de dispositivo para conectar dispositivos, que não podem ser conectados diretamente. A ponte de dispositivo IoT Central é uma solução de software livre e você pode encontrar os detalhes completos [aqui](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 


## <a name="iot-central-platform"></a>Plataforma IoT Central

O Azure IoT Central é uma plataforma que simplifica a criação de sua solução de IoT e ajuda a reduzir a carga e os custos de gerenciamento, operações e desenvolvimento de IoT. Com o IoT Central, você pode facilmente conectar, monitorar e gerenciar seus ativos de Internet das Coisas (IoT) em escala. Depois de conectar seus medidores inteligentes ao IoT Central, o modelo de aplicativo usa recursos internos, como modelos de dispositivo, comandos e painéis. O modelo de aplicativo também usa o armazenamento de IoT Central para cenários de caminho quente, como monitoramento de dados de medidor, análise, regras e visualização quase em tempo real. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Opções de extensibilidade para criar com IoT Central
A plataforma IoT Central fornece duas opções de extensibilidade: CDE (exportação de dados contínuas) e APIs. Os clientes e parceiros podem escolher entre essas opções com base para personalizar suas soluções para necessidades específicas. Por exemplo, um de nossos parceiros configuraram o CDE com Azure Data Lake Storage (ADLS). Eles estão usando ADLS para retenção de dados de longo prazo e outros cenários de armazenamento de caminho frio, tais processos de processamento em lote, auditoria e relatórios. 

## <a name="next-steps"></a>Passos seguintes

* Agora que você aprendeu sobre a arquitetura, [crie um aplicativo de medidor inteligente gratuitamente](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Para saber mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/)
