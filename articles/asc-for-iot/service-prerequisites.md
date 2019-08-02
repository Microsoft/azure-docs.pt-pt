---
title: Pré-requisitos da central de segurança do Azure para IoT | Microsoft Docs
description: Detalhes de tudo o que é necessário para começar a usar a central de segurança do Azure para os pré-requisitos do serviço de IoT.
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
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596825"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Pré-requisitos da central de segurança do Azure para IoT

Este artigo fornece uma explicação dos diferentes blocos de construção da central de segurança do Azure (ASC) para o serviço de IoT, o que você precisa para começar e explica os conceitos básicos para ajudar a entender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Camada padrão do Hub IoT
    - Privilégios de nível de **proprietário** da função RBAC 
- [Espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Central de segurança do Azure (recomendado)
    - O uso da central de segurança do Azure é uma recomendação, e não um requisito. Sem a central de segurança do Azure, você não poderá exibir os outros recursos do Azure no Hub IoT. 
 
## <a name="working-with-asc-for-iot-service"></a>Trabalhando com o serviço ASC para IoT

O ASC para informações e relatórios de IoT está disponível usando o Hub IoT do Azure e a central de segurança do Azure. Para habilitar o ASC para IoT no Hub IoT do Azure, é necessária uma conta com privilégios de nível de **proprietário** . Depois de habilitar o ASC para IoT em seu hub IoT, o ASC for IoT insights será exibido como o recurso de **segurança** no Hub IOT do Azure e como **IOT** na central de segurança do Azure. 

## <a name="supported-service-regions"></a>Regiões de serviço com suporte 

O ASC para IoT tem suporte no momento para hubs IoT nas seguintes regiões do Azure:
  - EUA Central  
  - East US 
  - EUA Leste 2
  - EUA Centro-Oeste
  - EUA Oeste
  - E.U.A. Oeste 2
  - EUA Central Sul
  - EUA Centro-Norte
  - Canadá Central
  - Leste do Canadá 
  - Europa do Norte    
  - Sul do Brasil
  - França Central  
  - Reino Unido Oeste 
  - Reino Unido Sul
  - Europa Ocidental 
  - Norte da Europa 
  - Oeste do Japão  
  - Leste do Japão  
  - Sudeste da Austrália
  - Leste da Austrália
  - Ásia Oriental   
  - Sudeste Asiático
  - Coreia do Sul Central
  - Coreia do Sul 
  - Índia Central
  - Sul da Índia
  
## <a name="wheres-my-iot-hub"></a>Onde está meu Hub IoT?

Verifique o local do Hub IoT para verificar a disponibilidade do serviço antes de começar. 

1. Abra o Hub IoT. 
2. Clique em **Descrição geral**. 
3. Verifique se o local listado corresponde a uma das [regiões de serviço com suporte](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas com suporte para agentes 

O ASC para agentes de IoT dá suporte a uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas com suporte](how-to-deploy-agent.md) para verificar sua biblioteca de dispositivos existente ou planejada.  

## <a name="next-steps"></a>Passos Seguintes
- Leia a [visão geral](overview.md) da segurança de IOT do Azure
- Saiba como [habilitar o serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes sobre a central de segurança do Azure para IOT](resources-frequently-asked-questions.md)
- Explore como [entender a central de segurança do Azure para alertas de IOT](concept-security-alerts.md)
