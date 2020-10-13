---
title: Explicação conceptual dos fundamentos do Módulo de Segurança para O Azure RTOS
description: Saiba o básico sobre o Módulo de Segurança para conceitos rtos azure e fluxo de trabalho.
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
ms.openlocfilehash: 9f816b35fd79d3f158ea12fd2c7e1d811823dc8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939507"
---
# <a name="security-module-for-azure-rtos-preview"></a>Módulo de Segurança para Azure RTOS (pré-visualização)

Utilize este artigo para obter uma melhor compreensão do Módulo de Segurança para Azure RTOS, incluindo funcionalidades e benefícios, bem como ligações à configuração e recursos de referência relevantes. 

## <a name="azure-rtos-iot-security-module"></a>Módulo de segurança Azure RTOS IoT

O Módulo de Segurança para O Azure RTOS fornece uma solução de segurança abrangente para dispositivos Azure RTOS como parte da oferta netX Duo. Dentro da oferta netX Duo, a Azure RTOS envia com o Módulo de Segurança Azure IoT incorporado, e fornece cobertura para ameaças comuns nos seus dispositivos do sistema operativo em tempo real uma vez ativados. 

O Módulo de Segurança para Azure RTOS funciona em segundo plano e proporciona uma experiência de utilizador perfeita, ao mesmo tempo que envia mensagens de segurança utilizando as ligações únicas de cada cliente ao seu IoT Hub. O Módulo de Segurança para Azure RTOS é ativado por padrão.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo é uma pilha de rede TCP/IP de qualidade industrial avançada projetada especificamente para aplicações em tempo real e IoT profundamente incorporadas. Azure RTOS NetX Duo é uma pilha de rede dual IPv4 e IPv6 que fornece um rico conjunto de protocolos, incluindo segurança e nuvem. Saiba mais sobre as soluções [Azure RTOS NetX Duo.](https://aka.ms/netxduo)

O módulo oferece as seguintes funcionalidades:

- **Detetar atividades de rede maliciosas**
- **Linhas de base de comportamento do dispositivo baseadas em alertas personalizados**
- **Melhorar a higiene de segurança do dispositivo**

## <a name="security-module-for-azure-rtos-architecture"></a>Módulo de Segurança para arquitetura Azure RTOS

O Módulo de Segurança para Azure RTOS é inicializado pela plataforma de middleware Azure IoT e utiliza clientes IoT Hub para enviar telemetria de segurança para o Hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagrama do estado do módulo de segurança Azure IoT e fluxo de informação":::

O Módulo de Segurança da Azure RTOS monitoriza a seguinte atividade e informação do dispositivo utilizando três colecionadores:
- Atividade da rede de **dispositivos TCP,** **UDP**e **ICM**
- Informações do sistema como as versões **Threadx** e **NetX Duo**
- Eventos de heartbeat

Cada colecionador está ligado a um grupo prioritário e cada grupo prioritário tem o seu próprio intervalo com possíveis valores de **Baixo,** **Médio**e **Alto.** Os intervalos afetam o intervalo de tempo em que os dados são recolhidos e enviados.

Cada intervalo de tempo é configurável e os conectores IoT podem ser ativados e desativados de forma a personalizar ainda mais a [sua solução](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Alertas e recomendações de segurança apoiados

O Módulo de Segurança do Azure RTOS suporta alertas e recomendações de segurança específicas. Certifique-se de [rever e personalizar os valores de alerta e recomendação relevantes](concept-rtos-security-alerts-recommendations.md) para o seu serviço após completar a configuração inicial.

## <a name="ready-to-begin"></a>Pronto para começar?

O Módulo de Segurança para Azure RTOS é fornecido como um download gratuito para os seus dispositivos IoT. O serviço de nuvem Defender for IoT está disponível com um teste de 30 dias por subscrição Azure. [Descarregue o módulo](https://github.com/azure-rtos/azure-iot-preview/releases) de segurança agora e vamos começar. 

## <a name="next-steps"></a>Passos seguintes

- Iniciou-se com o Módulo de Segurança para [pré-requisitos e configuração](quickstart-azure-rtos-security-module.md)Azure RTOS .
- Saiba mais sobre o Módulo de Segurança para alertas de segurança Azure RTOS [e suporte de recomendação.](concept-rtos-security-alerts-recommendations.md) 
- Utilize o Módulo de Segurança para Azure RTOS [referência API](azure-rtos-security-module-api.md).

