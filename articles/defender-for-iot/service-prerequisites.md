---
title: Componentes & pré-requisitos
description: Detalhes de tudo o que era necessário para começar com o Azure Defender para pré-requisitos do serviço IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089184"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender para pré-requisitos de IoT

Este artigo fornece uma explicação das diferentes componentes do serviço Defender para IoT, o que precisa de começar, e explica os conceitos básicos para ajudar a compreender o serviço.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Monitorização sem agentes para dispositivos IoT e OT (com base na tecnologia CyberX)
    - Rede comuta suporte à monitorização do tráfego através do porto SPAN
    - Aparelhos de hardware para sensor NTA, para mais informações consulte [hardware certificado](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Papel de **colaborador de** subscrição Azure (exigido apenas durante o embarque para a definição de dispositivos comprometidos)
    - IoT Hub (nível livre ou standard) **função contributiva** (para gestão conectada à nuvem)
- Segurança para dispositivos IoT geridos geridos através do Azure IoT Hub
    - IoT Hub (nível padrão) **Função contributiva**
    - IoT Hub: **Azure Defender for IoT** feature toggle deve ser ativado
    - Para suporte de módulo de segurança de nível de dispositivo  
        - Defender para agentes IoT suporta uma lista crescente de dispositivos e plataformas, ver a [lista de plataformas suportadas](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Regiões de serviço apoiadas

Consulte [as regiões apoiadas pelo IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) para obter mais informações. 

O Defensor da IoT encaminha todo o tráfego de todas as regiões europeias para o centro de dados regionais da Europa Ocidental e todas as regiões restantes para o centro de dados regional dos EUA central.

## <a name="next-steps"></a>Passos seguintes

- Leia a [Visão Geral](overview.md) de Segurança do Azure IoT
- Saiba como [Ativar o serviço](quickstart-onboard-iot-hub.md)
- Leia o [Defender para IoT FAQ](resources-frequently-asked-questions.md)
- Explore como [entender o Defender para alertas IoT](concept-security-alerts.md)
