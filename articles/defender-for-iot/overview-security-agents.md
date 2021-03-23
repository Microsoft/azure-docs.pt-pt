---
title: Agentes de segurança
description: Começa com a compreensão, configuração, implantação e utilização do Azure Defender para agentes de segurança IoT nos seus dispositivos IoT.
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783494"
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
- [Criar Defender-IoT-micro-agentes](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](how-to-deploy-agent.md)