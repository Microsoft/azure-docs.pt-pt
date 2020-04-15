---
title: Módulo de segurança para IoT Edge
description: Compreenda a arquitetura e as capacidades do Azure Security Center para módulo de segurança IoT para IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310632"
---
# <a name="azure-iot-edge-security-module"></a>Módulo de segurança Azure IoT Edge

[O Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) oferece capacidades poderosas para gerir e realizar fluxos de trabalho de negócios no limite.
A parte-chave que o IoT Edge desempenha em ambientes IoT torna-o particularmente atraente para atores mal-intencionados.

O Azure Security Center para o módulo de segurança IoT fornece uma solução de segurança abrangente para os seus dispositivos IoT Edge.
O Azure Security Center for IoT module recolhe, agrega e analisa dados de segurança bruta do seu Sistema Operativo e sistema de contentores em recomendações e alertas de segurança acionáveis.

À semelhança do Azure Security Center para agentes de segurança IoT para dispositivos IoT, o Azure Security Center for IoT Edge módulo é altamente personalizável através do seu módulo twin.
Consulte [a Configuração do seu agente](how-to-agent-configuration.md) para saber mais.

O Azure Security Center para módulo de segurança IoT para IoT Edge oferece as seguintes funcionalidades:

- Recolhe eventos de segurança bruta do Sistema Operativo subjacente (Linux) e dos sistemas IoT Edge Container.

  Consulte o Azure Security Center para a configuração do [agente IoT](how-to-agent-configuration.md) para saber mais sobre os colecionadores de dados de segurança disponíveis.

- Análise dos manifestos de implantação do IoT Edge.

- Agrega eventos de segurança bruta em mensagens enviadas através [do IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova a configuração através da utilização do módulo de segurança twin.

  Consulte o Configure um Centro de Segurança Azure para o [agente IoT](how-to-agent-configuration.md) para saber mais.

O Azure Security Center para módulo de segurança IoT para IoT Edge funciona em modo privilegiado sob IoT Edge.
É necessário um modo privilegiado para permitir que o módulo monitorize o Sistema Operativo e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas suportadas por módulos

O Azure Security Center para módulo de segurança IoT para IoT Edge está atualmente disponível apenas para Linux.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a arquitetura e capacidades do Azure Security Center para módulo de segurança IoT para IoT Edge.

Para continuar a começar com o Azure Security Center para a implantação de IoT, use os seguintes artigos:

- Implementar [módulo de segurança para IoT Edge](how-to-deploy-edge.md)
- Saiba como [configurar o seu módulo](how-to-agent-configuration.md) de segurança
- Reveja os [pré-requisitos](service-prerequisites.md) do Azure Security Center for IoT Service
- Saiba como ativar o Centro de [Segurança Azure para o serviço IoT no seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do Centro de [Segurança Azure para as FAQ IoT](resources-frequently-asked-questions.md)
