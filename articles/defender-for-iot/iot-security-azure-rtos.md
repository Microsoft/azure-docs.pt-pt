---
title: Defender-IoT-micro-agente para visão geral do Azure RTOS
description: Saiba mais sobre o defender-ioT-micro-agente para suporte e implementação Azure RTOS como parte do Azure Defender para IoT.
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785908"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Visão geral: Defender-IoT-micro-agente IoT para Azure RTOS (pré-visualização)

O Azure Defender para micro módulo IoT fornece uma solução de segurança abrangente para dispositivos que utilizam O Azure RTOS. Fornece cobertura para ameaças comuns e potenciais atividades maliciosas em dispositivos do sistema operativo em tempo real (RTOS). A Azure RTOS agora envia com o Azure IoT Defender-IoT-micro-agente incorporado.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualização do Defender para IoT Azure RTOS.":::


O micro módulo para Azure RTOS oferece as seguintes funcionalidades:

- Deteção maliciosa de atividades de rede
- Baseamento de comportamento do dispositivo personalizado baseado em alerta
- Melhor higiene da segurança do dispositivo

## <a name="detect-malicious-network-activities"></a>Detetar atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorizada. Os protocolos suportados são TCP, UDP e ICMP no IPv4 e IPv6. O Defender for IoT inspeciona cada uma destas atividades de rede contra o feed de inteligência de ameaça da Microsoft. O feed é atualizado em tempo real com milhões de indicadores de ameaça únicos recolhidos em todo o mundo.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseamento do comportamento do dispositivo com base em alertas personalizados

A base permite o agrupamento de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT são tipicamente projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que define o seu comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base despoleta um alerta.

## <a name="improve-your-device-security-hygiene"></a>Melhore a higiene de segurança do seu dispositivo

Ao utilizar a infraestrutura recomendada que o Defender for IoT fornece, pode obter conhecimentos e insights sobre questões no seu ambiente que impactem e danificam a postura de segurança dos seus dispositivos. Uma postura de segurança fraca do dispositivo IoT pode permitir que potenciais ataques tenham sucesso se ficarem inalterados. A segurança é sempre medida pelo elo mais fraco dentro de qualquer organização.

## <a name="get-started-protecting-azure-rtos-devices"></a>Começar a proteger dispositivos Azure RTOS

O defender-ioT-micro-agente para Azure RTOS é fornecido como um download gratuito para os seus dispositivos. O serviço de nuvem Defender for IoT está disponível com um teste de 30 dias por subscrição Azure. Para começar, descarregue o [defender-ioT-micro-agente para Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o Defender-IoT-micro-agente para Azure RTOS. Para saber mais sobre o defender-ioT-micro-agente e começar, consulte os seguintes artigos:

- [Conceitos de Azure RTOS IoT Defender-IoT-micro-agente](concept-rtos-security-module.md)
- [Quickstart: Azure RTOS IoT Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
