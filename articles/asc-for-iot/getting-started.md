---
title: Opções de implementação
description: Iniciou a compreensão do fluxo básico de trabalho do Azure Security Center para funcionalidades e serviços IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 452372f187e97a95b9eee88936b15a0409dd4fe0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311378"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Introdução ao Centro de Segurança do Azure para IoT

Este artigo fornece uma explicação dos diferentes componentes do Centro de Segurança Azure para o serviço IoT e explica como começar com o serviço usando duas opções de implementação possíveis.

## <a name="deployment-options"></a>Opções de implementação

Escolha o cenário de serviço que melhor satisfaça os requisitos do seu dispositivo IoT e ambiente.

### <a name="built-in-deployment"></a>Implantação embuta

Utilizando a opção de implementação incorporada e sem emenda, o Azure Security Center for IoT pode ser rapidamente integrado no seu Hub IoT e fornecer análises de segurança da configuração do hub IoT, identidade e gestão do dispositivo e padrões de comunicação do dispositivo hub.

Inicie uma [implantação incorporada](iot-hub-integration.md) com monitorização e recomendações do IoT Hub.
    <br>

### <a name="enhanced-deployment"></a>Implantação melhorada

Para melhorar as capacidades de segurança, a implementação do Azure Security Center para agentes IoT, além de permitir a segurança do IoT Hub fornece a recolha, análise e deteção de ameaças baseadas em agentes dos principais dados de segurança dos seus dispositivos IoT, bem como capacidades abrangentes de gestão da postura de segurança.

Inicie uma [implementação melhorada](security-agents.md) com uma solução de proteção abrangente de ameaças e de gestão de postura de segurança baseada em agentes.

## <a name="next-steps"></a>Passos seguintes

- Ativar [o Centro de Segurança Azure para IoT](quickstart-onboard-iot-hub.md)
- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](how-to-deploy-agent.md)
