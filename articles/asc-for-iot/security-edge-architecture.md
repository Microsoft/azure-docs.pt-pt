---
title: Noções básicas sobre o Centro de segurança do Azure para o módulo de segurança de IoT para o IoT Edge | Documentos da Microsoft
description: Compreenda a arquitetura e as capacidades do Centro de segurança do Azure para o módulo de segurança de IoT para o IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 847d6238bd42dc7da723dcc9acd47ed09c16dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200585"
---
# <a name="azure-iot-edge-security-module"></a>Módulo de segurança do Azure IoT Edge

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para worklo§1ads de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[O Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) fornece capacidades poderosas para gerir e executar fluxos de trabalho do negócio na periferia.
A parte de chave do IoT Edge desempenha em ambientes de IoT tornam especialmente atraente para atores maliciosos.

Centro de segurança do Azure (ASC) para o módulo de segurança de IoT fornece uma solução de segurança abrangente para o IoT Edge dispositivos.
ASC para o módulo de IoT recolhe, agrega e analisa dados de segurança não processados de seu sistema operativo e o sistema de contentor em alertas e recomendações de segurança acionáveis.

Assim como ASC para os agentes de segurança de IoT para dispositivos de IoT, o ASC para o módulo do IoT Edge é altamente personalizável através do seu módulo duplo.
Ver [configurar o seu agente](how-to-agent-configuration.md) para saber mais.

ASC para o módulo de segurança de IoT para o IoT Edge oferece as seguintes funcionalidades:

- Recolhe eventos de segurança não processados do sistema operacional subjacente (Linux) e os sistemas de contentor do IoT Edge.
  
  Ver [ASC para configuração do agente de IoT](how-to-agent-configuration.md) para saber mais sobre os Coletores de dados de segurança disponíveis.

- Análise de manifestos de implantação do IoT Edge.

- Agrega eventos de segurança não processados em mensagens enviadas por meio [Hub do IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova configuração através da utilização do duplo do módulo de segurança.

  Ver [configurar uma ASC para o agente de IoT](how-to-agent-configuration.md) para saber mais.

ASC para o módulo de segurança de IoT para o IoT Edge é executado num modo com privilégios no IoT Edge.
Modo privilegiado é necessário para permitir que o módulo para monitorizar o sistema operativo e outros módulos do IoT Edge.

## <a name="agent-supported-platforms"></a>Plataformas de agentes suportados

ASC para o módulo de segurança de IoT para o IoT Edge só está atualmente disponível para Linux.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a arquitetura e as capacidades do ASC para o módulo de segurança de IoT para o IoT Edge.

Para continuar a introdução ao ASC para implementação de IoT, utilize os seguintes artigos:

- Implementar [módulo de segurança para o IoT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu módulo de segurança](how-to-agent-configuration.md)
- Reveja o ASC para IoT [pré-requisitos de serviço](service-prerequisites.md)
- Saiba como [ativar o ASC para serviço de IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço a partir do [ASC para FAQ de IoT](resources-frequently-asked-questions.md)