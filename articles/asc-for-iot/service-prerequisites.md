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
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299465"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Pré-requisitos da central de segurança do Azure para IoT

Este artigo fornece uma explicação dos diferentes blocos de construção da central de segurança do Azure para o serviço de IoT, o que você precisa começar e explica os conceitos básicos para ajudar a entender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Camada padrão do Hub IoT
    - Privilégios de nível de **proprietário** da função RBAC 
- [Espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Central de segurança do Azure (recomendado)
    - O uso da central de segurança do Azure é uma recomendação, e não um requisito. Sem a central de segurança do Azure, você não poderá exibir os outros recursos do Azure no Hub IoT. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Trabalhando com a central de segurança do Azure para o serviço de IoT

A central de segurança do Azure para informações e relatórios de IoT está disponível usando o Hub IoT do Azure e a central de segurança do Azure. Para habilitar a central de segurança do Azure para IoT no Hub IoT do Azure, é necessária uma conta com privilégios de nível de **proprietário** . Depois de habilitar o ASC para IoT em seu hub IoT, a central de segurança do Azure para IoT insights é exibida como o recurso de **segurança** no Hub IOT do Azure e como **IOT** na central de segurança do Azure. 

## <a name="supported-service-regions"></a>Regiões de serviço com suporte 

A central de segurança do Azure para IoT atualmente tem suporte para hubs IoT nas seguintes regiões do Azure:
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

A central de segurança do Azure para IoT roteia todo o tráfego de todas as regiões européias para a Europa Ocidental data center regionais e todas as regiões restantes para o data center regional EUA Central.  
  
## <a name="wheres-my-iot-hub"></a>Onde está meu Hub IoT?

Verifique o local do Hub IoT para verificar a disponibilidade do serviço antes de começar. 

1. Abra o Hub IoT. 
2. Clique em **Descrição geral**. 
3. Verifique se o local listado corresponde a uma das [regiões de serviço com suporte](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas com suporte para agentes 

A central de segurança do Azure para agentes de IoT dá suporte a uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas com suporte](how-to-deploy-agent.md) para verificar sua biblioteca de dispositivos existente ou planejada.  

## <a name="next-steps"></a>Passos seguintes
- Leia a [visão geral](overview.md) da segurança de IOT do Azure
- Saiba como [habilitar o serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes sobre a central de segurança do Azure para IOT](resources-frequently-asked-questions.md)
- Explore como [entender a central de segurança do Azure para alertas de IOT](concept-security-alerts.md)
