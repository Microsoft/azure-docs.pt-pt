---
title: Introdução ao centro de segurança do Azure (ASC) para pré-visualização do IoT | Documentos da Microsoft
description: Comece a utilizar em compreender o fluxo de trabalho básico do ASC para recursos de IoT e o serviço.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a78eeda814b21d489eb957d44202b17c61d836d5
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541891"
---
# <a name="get-started-with-azure-security-center-asc-for-iot"></a>Começar com o Centro de segurança do Azure (ASC) de IoT 

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação dos blocos de construção diferentes do ASC para o serviço de IoT e explica como começar com [ativar o serviço](quickstart-onboard-iot-hub.md). 

ASC para IoT pode ser perfeitamente integrado no seu IoT Hub para fornecer análise de segurança de configuração do hub IoT, identidade de dispositivo e dispositivo de hub padrões de comunicação.
Para recursos de segurança avançada, o ASC para IoT fornece com base em agente de recolha de dados de segurança dos seus dispositivos IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC para integração de IoT Hub perfeita de IoT

Ao tentar proteger os seus dispositivos IoT individuais, a capacidade de recolher dados diretamente a partir dos dispositivos, ou da sua rede, é necessária. Para oferecer suporte a esse esforço, ASC para IoT oferece um arsenal de agentes de segurança de baixa capacidade para fornecer monitorização de dispositivos e o sistema de proteção.

No ASC para pré-visualização do IoT, arquitetura de referência para Linux e Windows agentes de segurança, em C# e C são fornecidos.
Os agentes de lidar com a recolha de eventos não processados do sistema operativo do dispositivo, agregação de eventos para reduzir custos e a configuração através de um dispositivo duplo de módulo.
Mensagens de segurança são enviadas através do IoT Hub, para o ASC para os serviços de análise de IoT.

## <a name="asc-for-iot-basics"></a>ASC para Noções básicas de IoT

Escolha o cenário de fluxo de trabalho que melhor satisfaz os requisitos de dispositivos e de ambiente de IoT:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Introdução ao ASC para integração de IoT Hub perfeita de IoT 

>[!Note]
>Este fluxo de trabalho permite-lhe utilizar o serviço sem utilizar o ASC para os agentes de segurança de IoT. 

Para ativar a monitorização do seu dispositivo gestão de identidade, dispositivo para nuvem e nuvem para padrões de comunicação do dispositivo, utilize a seguir o fluxo de trabalho básico para testes e para iniciar o serviço: 

1. [Ativar o ASC para o serviço de IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub tiver dispositivos não registados [registar um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Criar um módulo de segurança ascforiot](quickstart-create-security-twin.md) para os seus dispositivos. 
1. Definir o comportamento normal de dispositivo e do sistema por meio [alertas personalizados](quickstart-create-custom-alerts.md). 
1. Execute o sistema de teste para verificar o estado de serviço e dispositivo. 
1. Explore [alertas](concept-security-alerts.md), [recomendações](concept-recommendations.md), e [aprofunde-se utilizar o Log Analytics](how-to-security-data-access.md) através do IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Introdução ao ASC para agentes de segurança de IoT

Certifique-se de usar ASC para capacidades de segurança de IoT avançada, como monitorização ligações remotas, aplicações Active Directory, os eventos de início de sessão e práticas recomendadas de configuração de sistema operacional com o seguinte fluxo de trabalho básico para testar e ativar o serviço: 

1. [Ativar o ASC para o serviço de IoT ao seu IoT Hub](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub tiver dispositivos não registados [registar um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Criar um módulo de segurança azureiotsecurity](quickstart-create-security-twin.md) para os seus dispositivos.
1. Para instalar o agente num dispositivo simulado do Azure em vez de instalar num dispositivo real, [segurança de máquinas virtuais do Azure novo (VM) de rotação](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) numa zona de disponibilidade. 
1. [Implementar um ASC para o agente de segurança de IoT](tutorial-deploy-linux-cs.md) no seu dispositivo IoT ou nova VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) para executar uma simulação de um ataque inofensivo.
1. Verifique se o ASC para alertas de IoT em resposta ao ataque simulado no passo anterior. 
    - Inicie a verificação de cinco minutos depois de executar o script.
1. Explore [alertas](concept-security-alerts.md), [recomendações](concept-recommendations.md), e [aprofunde-se utilizar o Log Analytics](how-to-security-data-access.md) através do IoT Hub. 

## <a name="next-steps"></a>Passos Seguintes

- Ativar [ASC para IoT](quickstart-onboard-iot-hub.md)
- Configurar seu [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](select-deploy-agent.md)
