---
title: Defender para módulo de segurança IoT para IoT Edge
description: Compreenda a arquitetura e as capacidades do Azure Defender para o Módulo de Segurança IoT para IoT Edge.
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
ms.openlocfilehash: 1933e60892430b907e070ea04f39d8acc86ddac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937842"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Azure Defender para módulo de segurança IoT Edge

[O Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) fornece capacidades poderosas para gerir e executar fluxos de trabalho de negócio no limite.
A parte-chave que o IoT Edge desempenha em ambientes IoT torna-a particularmente atraente para atores mal-intencionados.

O módulo de segurança Defender for IoT fornece uma solução de segurança abrangente para os seus dispositivos IoT Edge.
O design Defender for IoT recolhe, agrega e analisa dados de segurança bruto do seu Sistema Operativo e sistema de contentores em recomendações e alertas de segurança acccionáveis.

Semelhante ao Defender para agentes de segurança IoT para dispositivos IoT, o módulo Defender for IoT Edge é altamente personalizável através do seu módulo twin.
Consulte [o Seu Agente](how-to-agent-configuration.md) para saber mais.

O módulo de segurança Defender for IoT para IoT Edge oferece as seguintes funcionalidades:

- Recolhe eventos de segurança bruto do Sistema Operativo subjacente (Linux) e dos sistemas de contentores IoT Edge.

  Consulte [a configuração do agente Defender para](how-to-agent-configuration.md) obter informações sobre os colecionadores de dados de segurança disponíveis.

- Análise dos manifestos de implantação do IoT Edge.

- Agrega eventos de segurança bruto em mensagens enviadas através [do IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova a configuração através da utilização do módulo de segurança twin.

  Consulte [configurar um Defensor para agente IoT](how-to-agent-configuration.md) para saber mais.

O módulo de segurança Defender for IoT Edge funciona num modo privilegiado sob ioT Edge.
O modo privilegiado é necessário para permitir que o módulo monitorize o Sistema Operativo e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas suportadas por módulos

O módulo de segurança Defender for IoT Para IoT Edge está atualmente disponível apenas para o Linux.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a arquitetura e capacidades do Defender para módulo de segurança IoT para IoT Edge.

Para continuar a trabalhar com o Defender para a implementação de IoT, utilize os seguintes artigos:

- Implementar [módulo de segurança para IoT Edge](how-to-deploy-edge.md)
- Saiba como [configurar o seu módulo de segurança](how-to-agent-configuration.md)
- Reveja os [pré-requisitos](service-prerequisites.md) do Defender for IoT Service
- Saiba como [ativar o serviço de IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Defender para IoT FAQ](resources-frequently-asked-questions.md)
