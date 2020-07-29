---
title: Componentes & pré-requisitos
description: Detalhes de tudo o que era necessário para começar com o Azure Security Center para pré-requisitos de serviço IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19e4ea37aa10c90d15a2b7dcdf962c131c8e473d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193222"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Centro de Segurança Azure para pré-requisitos de IoT

Este artigo fornece uma explicação dos diferentes componentes do Centro de Segurança Azure para o serviço IoT, o que precisa de começar, e explica os conceitos básicos para ajudar a entender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- IoT Hub Standard tier
  - Rbac fun **Privilégios de** nível proprietário
- [Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Centro de Segurança Azure (recomendado)
  - O uso do Centro de Segurança Azure é uma recomendação, e não um requisito. Sem o Centro de Segurança Azure, não poderá ver os seus outros recursos Azure dentro do IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Trabalhar com o Azure Security Center para o serviço IoT

O Azure Security Center para informações e relatórios IoT estão disponíveis usando o Azure IoT Hub e o Azure Security Center. Para ativar o Azure Security Center para IoT no seu Azure IoT Hub, é necessária uma conta com privilégios de nível **proprietário.** Depois de permitir o ASC para IoT no seu IoT Hub, o Azure Security Center para insights IoT são apresentados como a funcionalidade **de Segurança** no Azure IoT Hub e como **IoT** no Azure Security Center.

## <a name="supported-service-regions"></a>Regiões de serviço apoiadas

O Azure Security Center for IoT é atualmente apoiado para ioT hubs nas seguintes regiões de Azure:

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
- Austrália Sudeste
- Leste da Austrália
- Ásia Leste
- Ásia Sudeste
- Coreia do Sul Central
- Sul da Coreia do Sul
- Índia Central
- Sul da Índia

O Centro de Segurança Azure para ioT liga todo o tráfego de todas as regiões europeias para o centro de dados regional da Europa Ocidental e todas as regiões restantes para o centro de dados regional dos EUA central.

## <a name="wheres-my-iot-hub"></a>Onde está o meu hub de IoT?

Consulte a localização do seu Hub IoT para verificar a disponibilidade do serviço antes de começar.

1. Abra o seu Hub IoT.
1. Clique em **Descrição geral**.
1. Verifique se o local listado corresponde a uma das [regiões de serviço apoiadas](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plataformas apoiadas para agentes

O Centro de Segurança Azure para agentes IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas suportadas](how-to-deploy-agent.md) para verificar a biblioteca de dispositivos existente ou planeada.

## <a name="next-steps"></a>Próximos passos

- Leia a [Visão Geral](overview.md) de Segurança do Azure IoT
- Saiba como [Ativar o serviço](quickstart-onboard-iot-hub.md)
- Leia o [Centro de Segurança Azure para as FAQ de IoT](resources-frequently-asked-questions.md)
- Explore como compreender o [Centro de Segurança Azure para alertas IoT](concept-security-alerts.md)
