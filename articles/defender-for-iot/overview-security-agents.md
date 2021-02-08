---
title: Agentes de segurança
description: Começa com a compreensão, configuração, implantação e utilização do Azure Defender para agentes de segurança IoT nos seus dispositivos IoT.
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
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: 61c7f1bddd40151aff2b1ca556045d34c4a1cc0d
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820828"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Começa com o Azure Defender para micro agentes de dispositivo IoT

O Defender for IoT security agents oferece capacidades de segurança reforçadas, como monitorizar as melhores práticas de configuração do sistema operativo. Assuma o controlo da proteção contra ameaças no campo do dispositivo e da postura de segurança com um único serviço.

Os Defensores dos agentes de segurança IoT lidam com a recolha de eventos brutos do sistema operativo do dispositivo, a agregação de eventos para reduzir os custos e a configuração através de um módulo de dispositivo twin. As mensagens de segurança são enviadas através do seu Hub IoT, para o Defender para serviços de análise IoT.

Utilize o seguinte fluxo de trabalho para implantar e testar o seu Defender para agentes de segurança IoT:

1. [Ative o serviço Defender para IoT no seu IoT Hub](quickstart-onboard-iot-hub.md).

1. Se o seu Hub IoT não tiver dispositivos registados, [registe um novo dispositivo](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Crie um módulo DefenderIotMicroAgent twin](quickstart-create-micro-agent-module-twin.md) para os seus dispositivos.

1. Para instalar o agente num dispositivo simulado Azure em vez de instalar num dispositivo real, [rode uma nova Máquina Virtual Azure (VM)](../virtual-machines/linux/quick-create-portal.md) numa zona disponível.

1. [Coloque um Defender para agente de segurança IoT](how-to-deploy-linux-cs.md) no seu dispositivo IoT ou novo VM.

1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) executar um evento de base de OS.

1. Verifique as recomendações do Defender para ioT em resposta à falha de verificação de linha de base de SO simulada no passo anterior. Comece a verificação 30 minutos depois de executar o script.

## <a name="next-steps"></a>Passos seguintes

- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](how-to-deploy-agent.md)