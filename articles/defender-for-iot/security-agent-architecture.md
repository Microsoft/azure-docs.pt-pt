---
title: 'Quickstart: Visão geral dos agentes de segurança'
description: Neste quickstart, aprenda a entender a arquitetura do agente de segurança para os agentes utilizados no Azure Defender para o serviço IoT.
ms.topic: quickstart
ms.date: 4/4/2021
ms.openlocfilehash: 0937cccb0335f4eee16ca3590babe9574320b89f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384990"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Quickstart: Arquitetura de referência de agente de segurança

O Azure Defender for IoT fornece arquitetura de referência para agentes de segurança que registam, processam, agregam e enviam dados de segurança através do IoT Hub.

Os agentes de segurança são projetados para trabalhar num ambiente IoT limitado, e são altamente personalizáveis em termos de valores que fornecem quando comparados com os recursos que consomem.

Os agentes de segurança suportam as seguintes funcionalidades:

- Autenticar com a identidade do dispositivo existente, ou uma identidade de módulo dedicada. Para saber mais, consulte [os métodos de autenticação do agente de segurança.](concept-security-agent-authentication-methods.md)

- Recolher eventos de segurança brutos do Sistema Operativo subjacente (Linux, Windows). Para saber mais sobre os colecionadores de dados de segurança disponíveis, consulte [defender para configuração de agente IoT](how-to-agent-configuration.md).

- Eventos de segurança bruto agregados em mensagens enviadas através do IoT Hub.

- Configure remotamente através da utilização do módulo **de azureiotsecurity** twin. Para saber mais, consulte [Configurar um Defender para agente IoT](how-to-agent-configuration.md).

O Defender for IoT Security agents é desenvolvido como projetos de código aberto, e estão disponíveis no GitHub:

- [Defensor do agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender para agente baseado em IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum

## <a name="agent-supported-platforms"></a>Plataformas apoiadas por agente

O Defender for IoT oferece diferentes agentes instaladores para Windows de 32 bits e 64 bits, e o mesmo para o Linux de 32 bits e 64 bits. Certifique-se de que tem o instalador de agente correto para cada um dos seus dispositivos de acordo com a seguinte tabela:

| Arquitetura | Linux | Windows | Detalhes |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64 bits | C# ou C | C# | Recomendamos a utilização do agente C para dispositivos com recursos de dispositivos mais restritos ou mínimos. |


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você tem uma visão geral de alto nível sobre o Defender-IoT-micro-agente arquitetura, e os instaladores disponíveis.
Para continuar a começar com o Defender para a implementação de IoT, 

> [!div class="nextstepaction"]
> [métodos de autenticação de agente de segurança](concept-security-agent-authentication-methods.md)
