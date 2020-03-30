---
title: Centro de Segurança Azure para pré-requisitos do IoT Microsoft Docs
description: Detalhes de tudo o que é necessário para começar com os pré-requisitos do Serviço De Segurança Azure para ioT.
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
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71299465"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Centro de Segurança Azure para pré-requisitos de IoT

Este artigo fornece uma explicação dos diferentes blocos de construção do Azure Security Center para o serviço IoT, o que você precisa começar, e explica os conceitos básicos para ajudar a entender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- IoT Hub Standard tier
    - RBAC papel **Privilégios** de nível proprietário 
- [Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Centro de Segurança Azure (recomendado)
    - A utilização do Azure Security Center é uma recomendação, e não um requisito. Sem o Azure Security Center, não poderá ver os seus outros recursos Azure dentro do IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Trabalhar com o Azure Security Center para o serviço IoT

O Azure Security Center for IoT insights and reporting estão disponíveis usando o Azure IoT Hub e o Azure Security Center. Para permitir o Azure Security Center for IoT no seu Hub Azure IoT, é necessária uma conta **com** privilégios de nível proprietário. Depois de permitir o ASC para IoT no seu Hub IoT, o Azure Security Center for IoT insights é apresentado como a funcionalidade **de Segurança** no Hub Azure IoT e como **IoT** no Azure Security Center. 

## <a name="supported-service-regions"></a>Regiões de serviço apoiadas 

O Azure Security Center for IoT é atualmente apoiado para hubs IoT nas seguintes regiões azure:
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
  - Europa ocidental 
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

O Azure Security Center for IoT encaminha todo o tráfego de todas as regiões europeias para o centro de dados regional da Europa Ocidental e todas as regiões restantes para o centro de dados regional dos EUA Central.  
  
## <a name="wheres-my-iot-hub"></a>Onde está o meu hub?

Verifique a sua localização do IoT Hub para verificar a disponibilidade do serviço antes de começar. 

1. Abra o seu Hub IoT. 
2. Clique em **Descrição geral**. 
3. Verifique se a localização listada corresponde a uma das [regiões de serviço suportadas](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas suportadas para agentes 

O Azure Security Center para agentes IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas suportadas](how-to-deploy-agent.md) para verificar a biblioteca de dispositivos existente ou planeada.  

## <a name="next-steps"></a>Passos seguintes
- Leia a [visão geral](overview.md) da Segurança Azure IoT
- Saiba como [ativar o serviço](quickstart-onboard-iot-hub.md)
- Leia o Centro de [Segurança Azure para as FAQ IoT](resources-frequently-asked-questions.md)
- Explore como compreender o [Centro de Segurança Azure para alertas IoT](concept-security-alerts.md)
