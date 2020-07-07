---
title: Arquitetura de soluções
description: Conheça o fluxo de informação no Centro de Segurança Azure para o serviço IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311755"
---
# <a name="azure-security-center-for-iot-architecture"></a>Arquitetura do Centro de Segurança do Azure para IoT

Este artigo explica a arquitetura funcional do sistema do Centro de Segurança Azure para a solução IoT.

## <a name="azure-security-center-for-iot-components"></a>Centro de Segurança Azure para componentes IoT

O Centro de Segurança Azure para IoT é composto pelos seguintes componentes:

- Integração do IoT Hub
- Agentes do dispositivo (opcional)
- Enviar mensagem de segurança SDK
- Gasoduto de análise

### <a name="azure-security-center-for-iot-workflows"></a>Centro de Segurança Azure para fluxos de trabalho IoT

Centro de Segurança Azure para trabalhos de IoT em um de dois fluxos de trabalho de recurso: Incorporado e Melhorado

### <a name="built-in"></a>Incorporado

No modo **Incorporado,** o Azure Security Center for IoT está ativado quando optar por ativar a opção **Segurança** no seu Hub IoT. Oferecendo monitorização, recomendações e alertas em tempo real, o modo incorporado oferece visibilidade de um único passo e segurança incomparável. O modo de construção não requer a instalação do agente em nenhum dispositivo e utiliza análises avançadas em atividades registadas para analisar e proteger o seu dispositivo de campo.

### <a name="enhanced"></a>Melhorada

No modo **Melhorado,** depois de ligar a opção **Segurança** no seu Hub IoT e instalar o Azure Security Center para agentes de dispositivos IoT nos seus dispositivos, os agentes recolhem, agregam e analisam eventos de segurança bruto dos seus dispositivos. Eventos de segurança bruta podem incluir ligações IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. O Centro de Segurança Azure para agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar a alta produção de rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, como o envio de apenas informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

![Arquitetura do Centro de Segurança do Azure para IoT](./media/architecture/azure-iot-security-architecture.png)

Agentes de dispositivos e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o Azure IoT Hub. O IoT Hub recolhe esta informação e reencaminha-a para o Centro de Segurança Azure para o serviço IoT.

Uma vez ativado o Centro de Segurança Azure para o serviço IoT, para além dos dados reencaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo Azure Security Center for IoT. Estes dados incluem registos de operação em nuvem de dispositivo, identidades do dispositivo e configuração do Hub. Toda esta informação ajuda a criar o Azure Security Center para o pipeline de análise IoT.

O Azure Security Center for IoT analytics pipeline também recebe fluxos adicionais de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O Azure Security Center for IoT todo o pipeline de análise funciona com todas as configurações do cliente feitas no serviço (tais como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Azure Security Center for IoT combina todos os fluxos de informação para gerar recomendações e alertas acccáveis. O oleoduto contém tanto regras personalizadas criadas por investigadores e especialistas de segurança, como modelos de machine learning que procuram o desvio do comportamento padrão do dispositivo e da análise de risco.

O Centro de Segurança Azure para recomendações e alertas IoT (saída de gasoduto analítico) é escrito para o espaço de trabalho Log Analytics de cada cliente. A inclusão dos eventos brutos no espaço de trabalho, bem como os alertas e recomendações permite investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detetadas.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre a arquitetura básica e fluxo de trabalho do Azure Security Center para solução IoT. Para saber mais sobre pré-requisitos, como começar e ativar a sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Centro de Segurança Azure para FAQ IoT](resources-frequently-asked-questions.md)
- [Alertas de segurança do Centro de Segurança do Azure para IoT](concept-security-alerts.md)
