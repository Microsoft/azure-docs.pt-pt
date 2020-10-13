---
title: Módulo de segurança para visão geral do Azure RTOS
description: Saiba mais sobre o Módulo de Segurança para suporte e implementação de RTOS Azure como parte do serviço Defender for IoT
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761860"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Visão geral: Defender para módulo de segurança IoT para Azure RTOS (pré-visualização)

O módulo de segurança Defender for IoT para RTOS fornece uma solução de segurança abrangente para dispositivos Azure RTOS. O Azure RTOS agora envia com o Módulo de Segurança Azure IoT incorporado e fornece cobertura para ameaças comuns e potenciais atividades maliciosas em dispositivos do sistema operativo em tempo real. 

![Defender para IoT Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Módulo de Segurança para Azure RTOS oferece as seguintes funcionalidades: 
- Deteção maliciosa de atividades de rede
- Baseamento de comportamento personalizado e personalizado do dispositivo
- Melhorar a higiene de segurança do dispositivo

## <a name="detection-of-malicious-network-activities"></a>Deteção de atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorizada (protocolos suportados: TCP, UDP, ICMP no IPv4 e IPv6). O Defender for IoT inspeciona cada uma destas atividades de rede contra o feed de Inteligência de Ameaças da Microsoft. O feed é atualizado em tempo real com milhões de indicadores de ameaça únicos recolhidos em todo o mundo. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseamento do comportamento do dispositivo com base em alertas personalizados

A base permite o agrupamento de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT são tipicamente projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que define o seu comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base, desencadeia um alerta. 

## <a name="improve-your-device-security-hygiene"></a>Melhore a higiene de segurança do seu dispositivo

Aproveitando a infraestrutura recomendada, o Defender for IoT fornece, obtenha conhecimento e insights sobre questões no seu ambiente que impactem e danifiquem a postura de segurança dos seus dispositivos. A fraca postura de segurança do dispositivo IoT pode permitir que potenciais ataques tenham sucesso se forem deixados inalterados, uma vez que a segurança é sempre medida pelo elo mais fraco dentro de qualquer organização. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Começar a proteger dispositivos Azure RTOS

O Módulo de Segurança para Azure RTOS é fornecido como um download gratuito para os seus dispositivos. O serviço de nuvem Defender for IoT está disponível com um teste de 30 dias por subscrição Azure. Descarregue o [Módulo de Segurança para Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) para começar. 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você tomou conhecimento sobre o Módulo de Segurança para o serviço Azure RTOS. Para saber mais sobre o módulo de segurança e começar, consulte os seguintes artigos:

- [Conceitos de módulo de segurança Azure RTOS IoT](concept-rtos-security-module.md)
- [Quickstart: Módulo de segurança Azure RTOS IoT](quickstart-azure-rtos-security-module.md)
