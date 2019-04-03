---
title: Noções básicas sobre o Centro de segurança do Azure para a arquitetura de solução de IoT pré-visualização | Documentos da Microsoft
description: Saiba mais sobre o fluxo de informações no Centro de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862370"
---
# <a name="azure-security-center-for-iot-architecture"></a>Centro de segurança do Azure para uma arquitetura de IoT

Este artigo explica a arquitetura de sistema funcional do Centro de segurança do Azure (ASC) para a solução de IoT. 

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC para componentes de IoT

ASC para IoT é composto pelos seguintes componentes:
- Agentes de dispositivo
- Enviar mensagem de segurança SDK
- Integração do IoT Hub
- Pipeline de análise
 
### <a name="asc-for-iot-workflow"></a>ASC para fluxo de trabalho de IoT

ASC para os agentes de dispositivo do IoT permite-lhe facilmente recolher eventos de segurança bruto dos seus dispositivos. Eventos de segurança não processados podem incluir ligações IP, criação de processo, inícios de sessão do utilizador e outras informações relevantes para a segurança. ASC para os agentes de dispositivo de IoT também lidar com agregação de eventos para ajudar a evitar o débito de rede elevado. Os agentes são altamente personalizáveis, permitindo que utilizá-los para tarefas específicas, como o envio de apenas informações importantes do SLA mais rápida, ou para agregar informações de segurança e o contexto em segmentos maiores, evitando os custos de serviço superior.
 
Agentes de dispositivo e outro uso de aplicativos a **do Azure ASC enviar mensagem de segurança SDK** para enviar informações de segurança para o IoT Hub do Azure. IoT Hub coleta essas informações e encaminhá-la para o ASC para o serviço de IoT.

Depois do ASC para o serviço de IoT é ativada, além dos dados reencaminhados, IoT Hub também envia todos os seus dados internos para análise pelo ASC para IoT. Estes dados incluem os registos de operações de dispositivo-nuvem, identidades de dispositivos e configuração do Hub. Todas essas informações ajuda a criar o ASC para o pipeline de análise de IoT.
 
ASC para o pipeline de análise de IoT também recebe fluxos de inteligência de ameaças adicionais de várias origens na Microsoft e Microsoft parceiros. O ASC para o pipeline de análise completa do IoT funciona com cada configuração de cliente efetuada no serviço (por exemplo, alertas personalizados e a utilização da mensagem de segurança do envio SDK).
 
Utilizar o pipeline de análise, o ASC para IoT combina todos os fluxos de informações para gerar recomendações acionáveis e alertas. O pipeline contém ambas as regras personalizadas criadas por pesquisadores de segurança e especialistas, bem como a procurar desvio de análise de comportamento e o risco do dispositivo padrão de modelos de machine learning.
 
ASC para IoT recomendações e alertas (saída de pipeline de análise) é escrito para a área de trabalho do Log Analytics de cada cliente. Incluindo os eventos não processados na área de trabalho, bem como alertas e recomendações permite que as investigações de aprofundamento e consultas com os detalhes exatos das atividades suspeitas detetadas.  

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a arquitetura básica e o fluxo de trabalho do ASC para solução de IoT. Para saber mais sobre os pré-requisitos, como começar e ativar a sua solução de segurança do IoT Hub, veja os artigos seguintes:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC para IoT FAQ](resources-frequently-asked-questions.md)
- [ASC para alertas de segurança de IoT](concept-security-alerts.md)

