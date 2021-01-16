---
title: Visão geral do agente de segurança
description: Começa com a compreensão, configuração, implantação e utilização do Azure Defender para agentes de serviço de segurança IoT nos seus dispositivos IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: 2b1cd131e578b1d16fabee99b8de536e4a48ece0
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247306"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Começa com o Azure Defender para agentes de segurança de dispositivos IoT

Os agentes de segurança Defender for IoT oferecem capacidades de segurança reforçadas, tais como monitorização de ligações remotas, aplicações ativas, eventos de login e configurações de sistema operativo as melhores práticas. Assuma o controlo da proteção contra ameaças no campo do dispositivo e da postura de segurança com um único serviço.

São fornecidas arquitetura de referência para agentes de segurança Linux e Windows, tanto em C# como em C. e C.

O Defender para os agentes de segurança IoT lida com a recolha de eventos brutos do sistema operativo do dispositivo, a agregação de eventos para reduzir os custos e a configuração através de um módulo de dispositivo twin. As mensagens de segurança são enviadas através do seu Hub IoT, para o Defender para serviços de análise IoT.

Utilize o seguinte fluxo de trabalho para implantar e testar o seu Defender para agentes de segurança IoT:

1. [Ativar o serviço Defender para IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
1. Se o seu Hub IoT não tiver dispositivos registados, [registe um novo dispositivo](../iot-accelerators/iot-accelerators-device-simulation-overview.md).
1. [Crie um módulo de segurança azureiot para](quickstart-create-security-twin.md) os seus dispositivos.
1. Para instalar o agente num dispositivo simulado Azure em vez de instalar num dispositivo real, [rode uma nova Máquina Virtual Azure (VM)](../virtual-machines/linux/quick-create-portal.md) numa zona disponível.
1. [Coloque um Defender para agente de segurança IoT](how-to-deploy-linux-cs.md) no seu dispositivo IoT ou novo VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) executar uma simulação inofensiva de um ataque.
1. Verifique os alertas do Defender para IoT em resposta ao ataque simulado no passo anterior. Comece a verificação cinco minutos depois de executar o script.
1. Explore [alertas,](concept-security-alerts.md) [recomendações](concept-recommendations.md)e [mergulho profundo usando o Log Analytics](how-to-security-data-access.md) usando o IoT Hub.

## <a name="next-steps"></a>Passos seguintes

- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](how-to-deploy-agent.md)