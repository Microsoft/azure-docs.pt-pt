---
title: Explicação conceptual dos fundamentos do Defender-IoT-micro-agente para o Azure RTOS
description: Saiba o básico sobre o defender-ioT-micro-agente para conceitos Azure RTOS e fluxo de trabalho.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 04a499f1feae630d3436c75ae2081413789c0ca3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494239"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-agente para Azure RTOS (pré-visualização)

Utilize este artigo para obter uma melhor compreensão do defender-ioT-micro-agente para Azure RTOS, incluindo funcionalidades e benefícios, bem como ligações à configuração e recursos de referência relevantes. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agente

O Defender-IoT-micro-agente da Azure RTOS fornece uma solução de segurança abrangente para dispositivos Azure RTOS como parte da oferta netX Duo. Dentro da oferta netX Duo, a Azure RTOS envia com o Azure IoT Defender-IoT-micro-agente incorporado, e fornece cobertura para ameaças comuns nos seus dispositivos do sistema operativo em tempo real uma vez ativados. 

O Defender-IoT-micro-agent para Azure RTOS funciona em segundo plano e proporciona uma experiência de utilizador perfeita, ao mesmo tempo que envia mensagens de segurança usando as ligações únicas de cada cliente ao seu IoT Hub. O Defender-IoT-micro-agente para Azure RTOS é ativado por padrão.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo é uma pilha de rede TCP/IP de qualidade industrial avançada projetada especificamente para aplicações em tempo real e IoT profundamente incorporadas. Azure RTOS NetX Duo é uma pilha de rede dual IPv4 e IPv6 que fornece um rico conjunto de protocolos, incluindo segurança e nuvem. Saiba mais sobre as soluções [Azure RTOS NetX Duo.](/azure/rtos/netx-duo/)

O módulo oferece as seguintes funcionalidades:

- **Detetar atividades de rede maliciosas**
- **Linhas de base de comportamento do dispositivo baseadas em alertas personalizados**
- **Melhorar a higiene de segurança do dispositivo**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-agente para a arquitetura Azure RTOS

O Defender-IoT-micro-agente para Azure RTOS é inicializado pela plataforma de middleware Azure IoT e utiliza clientes IoT Hub para enviar telemetria de segurança para o Hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-micro-agente diagrama e fluxo de informação":::

O Defender-IoT-micro-agente da Azure RTOS monitoriza a seguinte atividade e informação do dispositivo utilizando três colecionadores:
- Atividade da rede de **dispositivos TCP,** **UDP** e **ICM**
- Informações do sistema como as versões **Threadx** e **NetX Duo**
- Eventos de heartbeat

Cada colecionador está ligado a um grupo prioritário e cada grupo prioritário tem o seu próprio intervalo com possíveis valores de **Baixo,** **Médio** e **Alto.** Os intervalos afetam o intervalo de tempo em que os dados são recolhidos e enviados.

Cada intervalo de tempo é configurável e os conectores IoT podem ser ativados e desativados de forma a personalizar ainda mais a [sua solução](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Alertas e recomendações de segurança apoiados

O Defender-IoT-micro-agente da Azure RTOS suporta alertas e recomendações de segurança específicas. Certifique-se de [rever e personalizar os valores de alerta e recomendação relevantes](concept-rtos-security-alerts-recommendations.md) para o seu serviço após completar a configuração inicial.

## <a name="ready-to-begin"></a>Pronto para começar?

O defender-ioT-micro-agente para Azure RTOS é fornecido como um download gratuito para os seus dispositivos IoT. O serviço de nuvem Defender for IoT está disponível com um teste de 30 dias por subscrição Azure. [Descarregue o defender-ioT-micro-agente agora](https://github.com/azure-rtos/azure-iot-preview/releases) e vamos começar. 

## <a name="next-steps"></a>Passos seguintes

- Começa com o Defender-IoT-micro-agente para [pré-requisitos e configuração](quickstart-azure-rtos-security-module.md)do Azure RTOS .
- Saiba mais sobre os alertas de segurança Defender-IoT-micro-agent para alertas de segurança Azure RTOS [e suporte de recomendação](concept-rtos-security-alerts-recommendations.md). 
- Utilize o defender-ioT-micro-agente para [API de referência](azure-rtos-security-module-api.md)Azure RTOS .