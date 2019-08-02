---
title: Entendendo a central de segurança do Azure para o módulo de segurança de IoT para IoT Edge versão prévia | Microsoft Docs
description: Entenda a arquitetura e os recursos da central de segurança do Azure para o módulo de segurança de IoT para IoT Edge.
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
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596850"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge módulo de segurança

> [!IMPORTANT]
> A central de segurança do Azure para o serviço de IoT para IoT Edge está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/) fornece recursos poderosos para gerenciar e executar fluxos de trabalho de negócios na borda.
A parte importante que IoT Edge desempenha em ambientes de IoT o torna particularmente atraente para atores mal-intencionados.

A central de segurança do Azure para o módulo de segurança de IoT fornece uma solução de segurança abrangente para seus dispositivos IoT Edge.
A central de segurança do Azure para o módulo IoT coleta, agrega e analisa dados de segurança brutos do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.

Semelhante à central de segurança do Azure para agentes de segurança de IoT para dispositivos IoT, a central de segurança do Azure para IoT Edge módulo é altamente personalizável por meio de seu módulo.
Consulte [configurar seu agente](how-to-agent-configuration.md) para saber mais.

A central de segurança do Azure para o módulo de segurança de IoT para IoT Edge oferece os seguintes recursos:

- Coleta eventos de segurança brutos do sistema operacional subjacente (Linux) e os sistemas de contêineres IoT Edge.
  
  Consulte a [central de segurança do Azure para configuração do agente de IOT](how-to-agent-configuration.md) para saber mais sobre os coletores de dados de segurança disponíveis.

- Análise de manifestos de implantação do IoT Edge.

- Agrega eventos de segurança brutos em mensagens enviadas por meio do [Hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova a configuração por meio do uso do módulo de segurança.

  Consulte [Configurar uma central de segurança do Azure para o agente de IOT](how-to-agent-configuration.md) para saber mais.

A central de segurança do Azure para o módulo de segurança de IoT para IoT Edge é executada em um modo privilegiado em IoT Edge.
O modo privilegiado é necessário para permitir que o módulo monitore o sistema operacional e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas com suporte do módulo

A central de segurança do Azure para o módulo de segurança de IoT para IoT Edge está disponível no momento apenas para Linux. 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu sobre a arquitetura e os recursos da central de segurança do Azure para o módulo de segurança de IoT para IoT Edge.

Para continuar a introdução à central de segurança do Azure para implantação de IoT, use os seguintes artigos:

- Implantar o [módulo de segurança para IOT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu módulo de segurança](how-to-agent-configuration.md)
- Examine os pré-requisitos da central de segurança do Azure para [Serviços](service-prerequisites.md) de IOT
- Saiba como [habilitar a central de segurança do Azure para o serviço de IOT em seu hub IOT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço da [central de segurança do Azure para as perguntas frequentes sobre IOT](resources-frequently-asked-questions.md)