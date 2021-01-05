---
title: Arquitetura de agente de segurança
description: Compreenda a arquitetura do agente de segurança para os agentes usados no Azure Defender para o serviço IoT.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835130"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência de agente de segurança

O Azure Defender for IoT fornece arquitetura de referência para agentes de segurança que registam, processam, agregam e enviam dados de segurança através do IoT Hub.

Os agentes de segurança são projetados para trabalhar num ambiente IoT limitado, e são altamente personalizáveis em termos de valores que fornecem quando comparados com os recursos que consomem.

Os agentes de segurança suportam as seguintes funcionalidades:

- Recolher eventos de segurança brutos do Sistema Operativo subjacente (Linux, Windows). Para saber mais sobre os colecionadores de dados de segurança disponíveis, consulte [defender para configuração de agente IoT](how-to-agent-configuration.md).

- Eventos de segurança bruto agregados em mensagens enviadas através do IoT Hub.

- Autenticar com a identidade do dispositivo existente, ou uma identidade de módulo dedicada. Consulte [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configure remotamente através da utilização do módulo **de azureiotsecurity** twin. Para saber mais, consulte [Configure um Defender para agente IoT](how-to-agent-configuration.md).

Os agentes de segurança do Defender para ioT são desenvolvidos como projetos de código aberto, e estão disponíveis no GitHub:

- [Defensor do agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender para agente baseado em IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas apoiadas por agente

O Defender for IoT oferece diferentes agentes instaladores para Windows 32bit e 64bit, e o mesmo para 32bit e 64bit Linux. Certifique-se de que tem o instalador de agente correto para cada um dos seus dispositivos de acordo com a seguinte tabela:

| Arquitetura | Linux | Windows | Detalhes |
|--|--|--|--|
| 32bit | C | C# |  |
| 64bit | C# ou C | C# | Recomendamos a utilização do agente C para dispositivos com recursos de dispositivos mais restritos ou mínimos. |


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o Defender para a arquitetura do agente de segurança IoT, e os instaladores disponíveis.

Para continuar a trabalhar com o Defender para a implementação de IoT, utilize os seguintes artigos:

- Compreender [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implemente um [agente de segurança](how-to-deploy-agent.md)
- Reveja os [pré-requisitos](quickstart-system-prerequisites.md) do sistema Defender for IoT
- Saiba como [ativar o serviço Defender para IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Defender para IoT FAQ](resources-frequently-asked-questions.md)
