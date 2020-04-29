---
title: Arquitetura de soluções
description: Conheça o fluxo de informação no Azure Security Center para o serviço IoT.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311755"
---
# <a name="azure-security-center-for-iot-architecture"></a>Arquitetura do Centro de Segurança do Azure para IoT

Este artigo explica a arquitetura funcional do sistema funcional do Azure Security Center para a solução IoT.

## <a name="azure-security-center-for-iot-components"></a>Centro de Segurança Azure para componentes IoT

O Azure Security Center for IoT é composto pelos seguintes componentes:

- Integração do Hub IoT
- Agentes de dispositivos (opcional)
- Enviar mensagem de segurança SDK
- Gasoduto de análise

### <a name="azure-security-center-for-iot-workflows"></a>Centro de Segurança Azure para fluxos de trabalho ioT

Azure Security Center for IoT trabalha num dos dois fluxos de trabalho: Incorporado e Melhorado

### <a name="built-in"></a>Incorporado

Em modo **incorporado,** o Azure Security Center for IoT está ativado quando opta por ativar a opção **de segurança** no seu Hub IoT. Oferecendo monitorização em tempo real, recomendações e alertas, o modo incorporado oferece visibilidade de dispositivo de um só passo e segurança inigualável. O modo de construção não requer a instalação do agente em nenhum dispositivo e utiliza análises avançadas em atividades registadas para analisar e proteger o seu dispositivo de campo.

### <a name="enhanced"></a>Melhorada

No modo **Enhanced,** depois de ligar a opção **De Segurança** no seu Hub IoT e instalar o Azure Security Center para agentes de dispositivos IoT nos seus dispositivos, os agentes recolhem, agregam e analisam eventos de segurança bruta a partir dos seus dispositivos. Eventos de segurança bruta podem incluir conexões IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. O Azure Security Center para agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar a alta produção de rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, tais como o envio de informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

![Arquitetura do Centro de Segurança do Azure para IoT](./media/architecture/azure-iot-security-architecture.png)

Os agentes do dispositivo e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o Hub Azure IoT. O IoT Hub recolhe esta informação e encaminha-a para o Centro de Segurança Azure para o serviço IoT.

Uma vez ativado o Azure Security Center for IoT, para além dos dados reencaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo Azure Security Center for IoT. Estes dados incluem registos de funcionamento da nuvem de dispositivo, identidades do dispositivo e configuração do Hub. Toda esta informação ajuda a criar o Azure Security Center para o oleoduto de análise IoT.

O Azure Security Center for IoT analytics pipeline também recebe streams adicionais de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O azure Security Center for IoT whole analytics pipeline trabalha com todas as configurações do cliente feitas no serviço (como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Azure Security Center for IoT combina todos os fluxos de informação para gerar recomendações e alertas atológicos. O pipeline contém ambas as regras personalizadas criadas por investigadores de segurança e especialistas, bem como modelos de aprendizagem automática que procuram desvio do comportamento padrão do dispositivo e análise de risco.

O Azure Security Center for IoT recomendações e alertas (saída de pipeline de análise) está escrito para o espaço de trabalho log Analytics de cada cliente. Incluindo os eventos brutos no espaço de trabalho, bem como os alertas e recomendações permitem investigações de mergulho profundo e consultas usando os detalhes exatos das atividades suspeitas detetadas.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre a arquitetura básica e fluxo de trabalho do Azure Security Center para a solução IoT. Para saber mais sobre os pré-requisitos, como começar e ativar a sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Centro de Segurança Azure para As FAQ iot](resources-frequently-asked-questions.md)
- [Alertas de segurança do Centro de Segurança do Azure para IoT](concept-security-alerts.md)
