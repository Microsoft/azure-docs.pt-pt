---
title: O que é arquitetura de solução baseada em agente
description: Saiba mais sobre o Azure Defender para arquitetura baseada em agentes IoT e fluxo de informação.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383529"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>O que é a solução baseada em agente para construtores de dispositivos

Este artigo descreve a arquitetura funcional do sistema do Defender para a solução baseada em agentes IoT. O Azure Defender for IoT oferece dois conjuntos de capacidades para adequar as necessidades do seu ambiente, solução sem agentes para organizações e solução baseada em agentes para construtores de dispositivos.

## <a name="iot-hub-built-in-security"></a>Segurança incorporada do hub IoT

O Defender for IoT é ativado por padrão em todos os novos Hub ioT que são criados. O Defender for IoT fornece monitorização, recomendações e alertas em tempo real, sem necessidade de instalação de agentes em quaisquer dispositivos e utiliza análises avançadas em dados de meta IoT Hub registados para analisar e proteger os seus dispositivos de campo e centros IoT. 

## <a name="defender-for-iot-micro-agent"></a>Defender para micro agente IoT 

O defensor do micro-agente IoT fornece proteção de segurança de profundidade e visibilidade no comportamento do dispositivo. recolhe, agrega e analisa eventos de segurança bruto dos seus dispositivos. Eventos de segurança bruta podem incluir ligações IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. O Defender para agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar o elevado rendimento da rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, como o envio de apenas informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

Agentes de dispositivos e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o hub Azure IoT. O hub IoT recebe esta informação e reencaminha-a para o Serviço Defender para ioT.

Uma vez ativado o serviço Defender for IoT, para além dos dados reencaminhados, o hub IoT também envia todos os seus dados internos para análise pelo Defender para IoT. Estes dados incluem registos de operação em nuvem de dispositivo, identidades do dispositivo e configuração do hub. Toda esta informação ajuda a criar o pipeline de análise do Defender para IoT.

O pipeline defender para análise ioT também recebe outros fluxos de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O pipeline de análise completo do Defender para IoT funciona com todas as configurações do cliente escamada no serviço (como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Defender for IoT combina todos os fluxos de informação para gerar recomendações e alertas acciáveis. O oleoduto contém tanto regras personalizadas criadas por investigadores e especialistas de segurança, como modelos de machine learning que procuram o desvio do comportamento padrão do dispositivo e da análise de risco.

O Defender para recomendações e alertas IoT (saída de gasoduto analítico) é escrito para o espaço de trabalho Log Analytics de cada cliente. Incluindo os eventos brutos no espaço de trabalho e os alertas e recomendações permite investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detetadas.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A arquitetura do micro-agente.":::

## <a name="next-steps"></a>Passos seguintes

[Defender para IoT FAQ](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
