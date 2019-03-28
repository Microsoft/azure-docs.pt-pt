---
title: ASC para a pré-visualização do IoT pré-requisitos | Documentos da Microsoft
description: Detalhes de todo o necessário para começar a utilizar o ASC para pré-requisitos de serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541831"
---
# <a name="asc-for-iot-prerequisites"></a>ASC para pré-requisitos de IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação dos blocos de construção diferentes do ASC para IoT serviço, o que precisa para começar e conceitos básicos para ajudar a compreender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Escalão Standard de Hub do IoT
    - Função RBAC **proprietário** privilégios do nível 
- [Área de trabalho do log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Centro de segurança do Azure (recomendado)
    - Embora o uso do Centro de segurança do Azure é apenas uma recomendação e não um requisito, sem ele, não será capaz de exibir os outros recursos dentro do IoT Hub do Azure. 
 
## <a name="working-with-asc-for-iot-service"></a>Trabalhar com ASC para o serviço de IoT

ASC para informações de IoT e relatórios estão disponíveis com o IoT Hub do Azure e o Centro de segurança do Azure. Para ativar o ASC para IoT no seu IoT Hub do Azure, uma conta com **proprietário** privilégios no nível de é obrigatório. Depois de ativar o ASC para IoT do seu IoT Hub, ASC para as informações de IoT são exibidos como o **Security** funcionalidade no IoT Hub do Azure e como **IoT** no Centro de segurança do Azure. 

## <a name="supported-service-regions"></a>Regiões de serviço com suporte 

ASC para IoT é atualmente suportada para os Hubs IoT nas seguintes regiões do Azure:
  - EUA Central
  - Europa do Norte
  - Sudeste Asiático

## <a name="wheres-my-iot-hub"></a>Onde está o meu Hub IoT?

Verifique a localização do IoT Hub para verificar a disponibilidade do serviço antes de começar. 

1. Abra o seu Hub IoT. 
2. Clique em **Descrição geral**. 
3. Certifique-se de que a localização listada corresponde a um da [serviço regiões suportadas](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas suportadas para agentes 

ASC para os agentes de IoT oferece suporte a uma lista crescente de dispositivos e plataformas. Consulte a [suportada a lista de plataformas](select-deploy-agent.md) para verificar a sua biblioteca de dispositivo planeado ou existente.  

## <a name="next-steps"></a>Passos Seguintes
- [Descrição geral](overview.md)
- [Ativar o serviço](quickstart-onboard-iot-hub.md)
- [ASC para IoT FAQ](resources-frequently-asked-questions.md)
- [Compreender o ASC para alertas de IoT](concept-security-alerts.md)
