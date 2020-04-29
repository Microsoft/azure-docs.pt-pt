---
title: Visão geral do agente de segurança
description: Inicie com compreensão, configuração, implantação e utilização do Azure Security Center para agentes de segurança IoT nos seus dispositivos IoT.
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
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310657"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Começar com o Azure Security Center para agentes de segurança de dispositivos IoT

O Azure Security Center para agentes de segurança IoT oferece capacidades de segurança melhoradas, tais como monitorização de ligações remotas, aplicações ativas, eventos de login e configuração do sistema operativo. Assuma o controlo da proteção contra ameaças no campo do dispositivo e a postura de segurança com um único serviço.

Arquitetura de referência para agentes de segurança Linux e Windows, tanto em C# como C são fornecidas.

O Azure Security Center para agentes de segurança IoT manuseia a recolha bruta de eventos do sistema operativo do dispositivo, a agregação de eventos para reduzir o custo e a configuração através de um módulo de dispositivo twin. As mensagens de segurança são enviadas através do seu IoT Hub, para o Centro de Segurança Azure para serviços de análise IoT.

Utilize o seguinte fluxo de trabalho para implantar e testar o seu Centro de Segurança Azure para agentes de segurança IoT:

1. [Habilitar o Centro de Segurança Azure para o serviço IoT para o seu Hub IoT](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub não tiver dispositivos registados, [registe um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Crie um módulo](quickstart-create-security-twin.md) de segurança de segurança para os seus dispositivos.
1. Para instalar o agente num dispositivo simulado Azure em vez de instalar num dispositivo real, [rode uma nova Máquina Virtual Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) numa zona disponível.
1. [Coloque um Centro de Segurança Azure para agente de segurança IoT](how-to-deploy-linux-cs.md) no seu dispositivo IoT, ou novo VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) para executar uma simulação inofensiva de um ataque.
1. Verifique o Centro de Segurança Azure para alertas IoT em resposta ao ataque simulado no passo anterior. Comece a verificação cinco minutos depois de executar o script.
1. Explore [alertas,](concept-security-alerts.md) [recomendações](concept-recommendations.md)e [mergulho profundo utilizando log analytics](how-to-security-data-access.md) usando o IoT Hub.

## <a name="next-steps"></a>Passos seguintes

- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implante um agente de segurança](how-to-deploy-agent.md)
