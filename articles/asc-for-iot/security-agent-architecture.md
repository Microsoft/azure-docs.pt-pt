---
title: Entendendo a central de segurança do Azure para a arquitetura do agente de segurança de IoT | Microsoft Docs
description: Entenda a arquitetura do agente de segurança para os agentes usados na central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596903"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência do agente de segurança

A central de segurança do Azure para IoT fornece arquitetura de referência para agentes de segurança que registram, processam, agregam e enviam dados de segurança por meio do Hub IoT.

Os agentes de segurança são projetados para funcionar em um ambiente de IoT restrito e são altamente personalizáveis em termos de valores que eles fornecem quando comparados aos recursos que consomem.

Os agentes de segurança oferecem suporte aos seguintes recursos:

- Coletar eventos de segurança brutos do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os coletores de dados de segurança disponíveis, consulte [central de segurança do Azure para configuração do agente de IOT](how-to-agent-configuration.md).

- Agregar eventos de segurança brutos em mensagens enviadas por meio do Hub IoT.

- Autentique com a identidade do dispositivo existente ou uma identidade de módulo dedicada. Consulte [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configure remotamente por meio do uso do módulo **azureiotsecurity** . Para saber mais, confira [Configurar uma central de segurança do Azure para o agente de IOT](how-to-agent-configuration.md).

A central de segurança do Azure para agentes de segurança de IoT é desenvolvida como projetos de código-fonte aberto e está disponível no GitHub: 

- [Central de segurança do Azure para o agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Central de segurança do Azure C#para agente baseado em IOT](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas com suporte do agente

A central de segurança do Azure para IoT oferece agentes de instalador diferentes para as janelas de 32 bits e de 64 bits e a mesma para Linux de 32 e 64 bits. Verifique se você tem o instalador do agente correto para cada um dos seus dispositivos de acordo com a tabela a seguir:

| Arquitetura | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bits  | C  | C#  ||
| 64 bits  | C#ou C           | C#      | É recomendável usar o agente do C para dispositivos com recursos de dispositivo mais restritos ou mínimos.|
|

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a central de segurança do Azure para a arquitetura do agente de segurança do IoT e os instaladores disponíveis.

Para continuar a introdução à central de segurança do Azure para implantação de IoT, use os seguintes artigos:

- Entender os [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecionar e implantar um [agente de segurança](how-to-deploy-agent.md)
- Examine os pré-requisitos da central de segurança do Azure para [Serviços](service-prerequisites.md) de IOT
- Saiba como [habilitar a central de segurança do Azure para o serviço de IOT em seu hub IOT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço da [central de segurança do Azure para as perguntas frequentes sobre IOT](resources-frequently-asked-questions.md)
