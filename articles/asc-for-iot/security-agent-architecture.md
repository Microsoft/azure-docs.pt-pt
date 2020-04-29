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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310677"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência de agente de segurança

O Azure Security Center for IoT fornece arquitetura de referência para agentes de segurança que registam, processam, agregam e enviam dados de segurança através do IoT Hub.

Os agentes de segurança são projetados para trabalhar num ambiente iot restrito, e são altamente personalizáveis em termos de valores que fornecem quando comparados com os recursos que consomem.

Os agentes de segurança suportam as seguintes funcionalidades:

- Colete eventos de segurança bruta do Sistema Operativo subjacente (Linux, Windows). Para saber mais sobre os colecionadores de dados de segurança disponíveis, consulte [o Azure Security Center para a configuração do agente IoT](how-to-agent-configuration.md).

- Agregar eventos de segurança bruta em mensagens enviadas através do IoT Hub.

- Autenticar com identidade de dispositivo existente, ou uma identidade de módulo dedicado. Consulte os métodos de [autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configure remotamente através da utilização do módulo de **segurança azul** twin. Para saber mais, consulte Configure um Centro de [Segurança Azure para agente IoT](how-to-agent-configuration.md).

O Azure Security Center for IoT Security agents é desenvolvido como projetos de código aberto, e estão disponíveis no GitHub:

- [Centro de Segurança Azure para agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Centro de Segurança Azure para iot c#agente baseado](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas apoiadas por agentes

O Azure Security Center for IoT oferece diferentes agentes instaladores para Windows de 32 bits e 64bits, e o mesmo para 32bits e 64bit Linux. Certifique-se de que dispõe do instalador de agente correto para cada um dos seus dispositivos de acordo com a tabela seguinte:

| Arquitetura | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C# ou C           | C#      | Recomendamos a utilização do agente C para dispositivos com recursos de dispositivos mais restritos ou mínimos.|
|

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você soube sobre o Azure Security Center para a arquitetura do agente de segurança IoT, e os instaladores disponíveis.

Para continuar a começar com o Azure Security Center para a implantação de IoT, use os seguintes artigos:

- Compreender os métodos de [autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](how-to-deploy-agent.md)
- Reveja os pré-requisitos do Centro de Segurança Azure para [os pré-requisitos](service-prerequisites.md) do serviço IoT
- Saiba como ativar o [Azure Security Center para o serviço IoT no seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do Centro de [Segurança Azure para as FAQ IoT](resources-frequently-asked-questions.md)
