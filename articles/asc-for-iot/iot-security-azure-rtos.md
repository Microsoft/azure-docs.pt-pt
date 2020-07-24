---
title: Suporte Azure RTOS
description: Saiba mais sobre o suporte da Azure RTOS no Centro de Segurança Azure para o serviço IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097754"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Centro de Segurança Azure para Solução de Segurança IoT para Azure RTOS 

O módulo de segurança Azure Security Center for IoT fornece uma solução de segurança abrangente para dispositivos Azure RTOS. Azure RTOS envia com um módulo de segurança incorporado que cobre ameaças comuns em dispositivos do sistema operativo em tempo real. 

![Centro de Segurança Azure para IoT Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


O Azure Security Center for IoT security module with Azure RTOS support oferece as seguintes funcionalidades: 
- Deteção maliciosa de atividades de rede
- Baseamento de comportamento personalizado e personalizado do dispositivo
- Melhorar a higiene de segurança do dispositivo

### <a name="detection-of-malicious-network-activities"></a>Deteção de atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorizada (protocolos suportados: TCP, UDP, ICMP no IPv4 e IPv6). O Azure Security Center for IoT inspeciona cada uma destas atividades de rede contra o feed da Microsoft Threat Intelligence. O feed é atualizado em tempo real com milhões de indicadores de ameaça únicos recolhidos em todo o mundo. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseamento do comportamento do dispositivo com base em alertas personalizados

A base permite o agrupamento de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT são tipicamente projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que define o seu comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base, desencadeia um alerta. 

### <a name="improve-your-device-security-hygiene"></a>Melhore a higiene de segurança do seu dispositivo

Aproveitando a infraestrutura recomendada, o Azure Security Center for IoT fornece, obtenha conhecimento e insights sobre questões no seu ambiente que impactem e danifiquem a postura de segurança dos seus dispositivos. A postura de segurança do dispositivo IoT pode permitir que potenciais ataques tenham sucesso se forem deixados inalterados, uma vez que a segurança é sempre medida pelo elo mais fraco dentro de qualquer organização. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Começar a proteger dispositivos Azure RTOS

- O Azure Security Center for IoT security module for Azure RTOS é fornecido como um download gratuito para os seus dispositivos. O Azure Security Center for IoT cloud service está disponível com um teste de 30 dias por subscrição de Azure. Descarregue o [Azure Security Center para o módulo de segurança IoT para que o Azure RTOS](https://github.com/azure-rtos/iot-security-module-preview) possa começar. 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o Azure Security Center para suporte IoT Azure RTOS. Para aprender a iniciar e ativar a sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Centro de Segurança Azure para FAQ IoT](resources-frequently-asked-questions.md)
- [Alertas de segurança do Centro de Segurança do Azure para IoT](concept-security-alerts.md)
