---
title: Arquitetura de soluções
description: Saiba mais sobre o fluxo de informação no Azure Defender para o serviço IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843418"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender para arquitetura IoT

Este artigo explica a arquitetura funcional do sistema do Defender para a solução IoT.

## <a name="defender-for-iot-components"></a>Defender para componentes IoT

O Defender for IoT é composto pelos seguintes componentes:

- Integração do IoT Hub
- Agentes do dispositivo (opcional)
- Enviar mensagem de segurança SDK
- Gasoduto de análise

### <a name="defender-for-iot-workflows"></a>Defender para fluxos de trabalho IoT

Defender para IoT funciona em um de dois fluxos de trabalho de recurso: Incorporado e Melhorado

### <a name="built-in"></a>Incorporado

No modo **Incorporado,** o Defender para IoT está ativado quando optar por ativar a opção **Segurança** no seu Hub IoT. Oferecendo monitorização, recomendações e alertas em tempo real, o modo incorporado oferece visibilidade de um único passo e segurança incomparável. O modo de construção não requer a instalação do agente em nenhum dispositivo e utiliza análises avançadas em atividades registadas para analisar e proteger o seu dispositivo de campo.

### <a name="enhanced"></a>Melhorada

No modo **Melhorado,** depois de ligar a opção **Segurança** no seu Hub IoT e instalar o Defender para agentes de dispositivos IoT nos seus dispositivos, os agentes recolhem, agregam e analisam eventos de segurança bruto dos seus dispositivos. Eventos de segurança bruta podem incluir ligações IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. Os defensores dos agentes de dispositivos IoT também lidam com a agregação de eventos para ajudar a evitar a alta produção de rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, como o envio de apenas informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

![Defensor da arquitetura IoT](./media/architecture/azure-iot-security-architecture.png)

Agentes de dispositivos e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o Azure IoT Hub. O IoT Hub recebe esta informação e reencaminha-a para o Serviço Defender para ioT.

Uma vez ativado o serviço Defender for IoT, para além dos dados reencaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo Defender para IoT. Estes dados incluem registos de operação em nuvem de dispositivo, identidades do dispositivo e configuração do Hub. Toda esta informação ajuda a criar o pipeline de análise do Defender para IoT.

O pipeline de análise de IoT também recebe fluxos adicionais de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O pipeline de análise completo do Defender para IoT funciona com todas as configurações do cliente escamada no serviço (como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Defender for IoT combina todos os fluxos de informação para gerar recomendações e alertas acciáveis. O oleoduto contém tanto regras personalizadas criadas por investigadores e especialistas de segurança, como modelos de machine learning que procuram o desvio do comportamento padrão do dispositivo e da análise de risco.

O Defender para recomendações e alertas IoT (saída de gasoduto analítico) é escrito para o espaço de trabalho Log Analytics de cada cliente. A inclusão dos eventos brutos no espaço de trabalho, bem como os alertas e recomendações permite investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detetadas.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre a arquitetura básica e fluxo de trabalho da solução Defender para ioT. Para saber mais sobre pré-requisitos, como começar e ativar a sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Defender para IoT FAQ](resources-frequently-asked-questions.md)
- [Alertas de segurança do Defender para IoT](concept-security-alerts.md)
