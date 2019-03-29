---
title: Compreender o ASC para arquitetura de agente de segurança de IoT pré-visualização | Documentos da Microsoft
description: Compreenda a arquitetura de agente de segurança para os agentes utilizados no ASC para o serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7de4307f7eef95a637dc828ac1738bd0ed39c7c7
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578463"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência do agente de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC para IoT fornece uma arquitetura de referência para os agentes de segurança que iniciar sessão, processam, agregam e enviam dados de segurança através do IoT hub.

Agentes de segurança foram concebidas para funcionar num ambiente de IoT restrito e são altamente personalizáveis em termos de valores fornecem em comparação com os recursos que consumirem.

Agentes de segurança suportam as seguintes funcionalidades:

- Recolha eventos de segurança não processados do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os recoletores de dados de segurança disponíveis, veja [ASC para configuração do agente de IoT](how-to-agent-configuration.md).

- Agregar eventos de segurança não processados em mensagens enviadas através do IoT hub.

- Autenticar com a identidade de dispositivo existente ou uma identidade de módulo dedicado. Ver [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configurar remotamente através da utilização do **azureiotsecurity** duplo do módulo. Para obter mais informações, consulte [configurar uma ASC para o agente de IoT](how-to-agent-configuration.md).

ASC para os agentes de segurança de IoT são desenvolvidas como projetos de código-fonte aberto e estão disponível a partir do GitHub: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas de agentes suportados

ASC para IoT oferece agentes diferentes de instalador de 32 bits e 64 bits Windows e o mesmo para 32 bits e 64 bits Linux. Certifique-se de que tem o instalador do agente correta para cada um dos seus dispositivos, de acordo com a tabela seguinte:

| 32 ou 64 bits | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bits  | C  | C#  ||
| 64 bits  | C#ou C           | C#      | Utilize o agente de C para dispositivos com recursos mínimos|

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre ASC para arquitetura de agente de segurança de IoT e os instaladores disponíveis.

Para continuar a introdução ao ASC para implementação de IoT, utilize os seguintes artigos:

- Compreender [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implementar um [agente de segurança](how-to-deploy-agent.md)
- Reveja o ASC para IoT [pré-requisitos de serviço](service-prerequisites.md)
- Saiba como [ativar o ASC para serviço de IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço a partir do [ASC para FAQ de IoT](resources-frequently-asked-questions.md)