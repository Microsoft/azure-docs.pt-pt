---
title: Visão geral do portfólio de agentes e suporte ao OS (Pré-visualização)
description: O Azure Defender for IoT fornece um grande portfólio de agentes com base no tipo de dispositivo.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: d2e463051d0897afe52981ea2d50ddd1f06bb54d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383427"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Visão geral do portfólio de agentes e suporte ao OS (Pré-visualização)

O Azure Defender for IoT fornece um grande portfólio de agentes com base no tipo de dispositivo. 

## <a name="standalone-agent"></a>Agente autónomo

O agente autónomo cobre a maioria dos sistemas operativos Linux, que podem ser implementados como um pacote binário ou como um código fonte que pode ser incorporado como parte do firmware e permitir modificação e personalização com base nas necessidades do cliente. Um exemplo de suporte ao os: 

| Sistema operativo | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Para mais detalhes, suporte ao sistema operativo ou para solicitar o acesso ao código fonte para que possa incorporá-lo como parte do firmware do dispositivo, contactar o seu gestor de conta ou enviar um e-mail para <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Micro-agente Azure RTOS

O Azure Defender para micro-agente IoT fornece uma solução de segurança abrangente e leve para dispositivos que utilizam O Azure RTOS. O Azure Defender for IoT micro agent fornece cobertura para ameaças comuns e potenciais atividades maliciosas em dispositivos do sistema operativo em tempo real (RTOS). O micro-agente vem integrado como parte do componente Azure RTOS NetX Duo e monitoriza a atividade da rede do dispositivo. 

O Azure Defender para micro-agente IoT vem integrado como parte do componente Azure RTOS NetX Duo e monitoriza a atividade da rede do dispositivo. O micro-agente consiste numa solução de segurança abrangente e leve que fornece cobertura para ameaças comuns e potenciais atividades maliciosas em dispositivos do sistema operativo em tempo real (RTOS).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a visão geral do [micro-agente autónomo (Preview)](concept-standalone-micro-agent-overview.md).