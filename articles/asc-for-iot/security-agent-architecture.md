---
title: Arquitetura de agente de segurança
description: Compreenda a arquitetura do agente de segurança para os agentes usados no Centro de Segurança Azure para o serviço IoT.
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
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310677"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência de agente de segurança

O Azure Security Center for IoT fornece arquitetura de referência para agentes de segurança que registam, processam, agregam e enviam dados de segurança através do IoT Hub.

Os agentes de segurança são projetados para trabalhar num ambiente IoT limitado, e são altamente personalizáveis em termos de valores que fornecem quando comparados com os recursos que consomem.

Os agentes de segurança suportam as seguintes funcionalidades:

- Recolher eventos de segurança brutos do Sistema Operativo subjacente (Linux, Windows). Para saber mais sobre os colecionadores de dados de segurança disponíveis, consulte [o Azure Security Center para a configuração do agente IoT](how-to-agent-configuration.md).

- Eventos de segurança bruto agregados em mensagens enviadas através do IoT Hub.

- Autenticar com a identidade do dispositivo existente, ou uma identidade de módulo dedicada. Consulte [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configure remotamente através da utilização do módulo **de azureiotsecurity** twin. Para saber mais, consulte [o Configure um Centro de Segurança Azure para agente IoT](how-to-agent-configuration.md).

O Centro de Segurança Azure para agentes de segurança IoT são desenvolvidos como projetos de código aberto, e estão disponíveis no GitHub:

- [Centro de Segurança Azure para agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Centro de Segurança Azure para agente baseado em IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas apoiadas por agente

O Azure Security Center for IoT oferece diferentes agentes instaladores para Windows 32bit e 64bit, e o mesmo para 32bit e 64bit Linux. Certifique-se de que tem o instalador de agente correto para cada um dos seus dispositivos de acordo com a seguinte tabela:

| Arquitetura | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C# ou C           | C#      | Recomendamos a utilização do agente C para dispositivos com recursos de dispositivos mais restritos ou mínimos.|
|

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu sobre o Azure Security Center para arquitetura de agente de segurança IoT, e os instaladores disponíveis.

Para continuar a trabalhar com o Azure Security Center para a implantação de IoT, utilize os seguintes artigos:

- Compreender [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implemente um [agente de segurança](how-to-deploy-agent.md)
- Reveja o Centro de Segurança Azure para [pré-requisitos](service-prerequisites.md) de serviço IoT
- Saiba como [ativar o Azure Security Center para o serviço IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)
