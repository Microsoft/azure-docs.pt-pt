---
title: Componentes & pré-requisitos
description: Detalhes de tudo o que era necessário para começar com o Azure Defender para pré-requisitos do serviço IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937837"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender para pré-requisitos de IoT

Este artigo fornece uma explicação das diferentes componentes do serviço Defender para IoT, o que precisa de começar, e explica os conceitos básicos para ajudar a compreender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- IoT Hub Standard tier
  - Papel azul **Privilégios de** nível do proprietário
- [Área de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Centro de Segurança Azure (recomendado)
  - O uso do Centro de Segurança Azure é uma recomendação, e não um requisito. Sem o Centro de Segurança Azure, não poderá ver os seus outros recursos Azure dentro do IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Trabalhar com o Defender para o serviço IoT

O Defender para informações e relatórios IoT está disponível usando o Azure IoT Hub e o Azure Security Center. Para ativar o Defender para IoT no seu Azure IoT Hub, é necessária uma conta com privilégios de nível **proprietário.** Depois de permitir o ASC para IoT no seu IoT Hub, o Defender para insights IoT são apresentados como a funcionalidade **de Segurança** no Azure IoT Hub e como  **IoT** no Azure Security Center.

## <a name="supported-service-regions"></a>Regiões de serviço apoiadas

O Defender for IoT é atualmente apoiado para os Hubs IoT nas seguintes regiões de Azure:

- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A. Oeste 2
- Centro dos EUA Sul
- E.U.A. Centro-Norte
- Canadá Central
- Leste do Canadá
- Europa do Norte
- Sul do Brasil
- França Central
- Oeste do Reino Unido
- Sul do Reino Unido
- Europa Ocidental
- Norte da Europa
- Oeste do Japão
- Leste do Japão
- Sudeste da Austrália
- Leste da Austrália
- Ásia Leste
- Sudeste Asiático
- Coreia do Sul Central
- Sul da Coreia do Sul
- Índia Central
- Sul da Índia

O Defensor da IoT encaminha todo o tráfego de todas as regiões europeias para o centro de dados regionais da Europa Ocidental e todas as regiões restantes para o centro de dados regional dos EUA central.

## <a name="wheres-my-iot-hub"></a>Onde está o meu hub de IoT?

Consulte a localização do seu Hub IoT para verificar a disponibilidade do serviço antes de começar.

1. Abra o seu Hub IoT.
1. Clique em **Descrição geral**.
1. Verifique se o local listado corresponde a uma das [regiões de serviço apoiadas](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plataformas apoiadas para agentes

O Defender para agentes IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas suportadas](how-to-deploy-agent.md) para verificar a biblioteca de dispositivos existente ou planeada.

## <a name="next-steps"></a>Passos seguintes

- Leia a [Visão Geral](overview.md) de Segurança do Azure IoT
- Saiba como [Ativar o serviço](quickstart-onboard-iot-hub.md)
- Leia o [Defender para IoT FAQ](resources-frequently-asked-questions.md)
- Explore como [entender o Defender para alertas IoT](concept-security-alerts.md)
