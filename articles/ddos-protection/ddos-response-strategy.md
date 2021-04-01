---
title: Componentes de uma estratégia de resposta a DDoS
description: Saiba o que usar o Azure DDoS Protection Standard para responder aos ataques do DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814062"
---
# <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Um ataque DDoS que visa os recursos do Azure geralmente requer uma intervenção mínima do ponto de vista do utilizador. Ainda assim, incorporar a mitigação do DDoS como parte de uma estratégia de resposta a incidentes ajuda a minimizar o impacto na continuidade do negócio.

## <a name="microsoft-threat-intelligence"></a>Inteligência de ameaça da Microsoft

A Microsoft tem uma extensa rede de inteligência de ameaças. Esta rede utiliza o conhecimento coletivo de uma comunidade de segurança alargada que suporta serviços online da Microsoft, parceiros microsoft e relações dentro da comunidade de segurança da Internet. 

Como fornecedor de infraestruturas críticas, a Microsoft recebe avisos antecipados sobre ameaças. A Microsoft recolhe informações sobre ameaças dos seus serviços online e da sua base global de clientes. A Microsoft incorpora toda esta inteligência de ameaça de volta aos produtos Azure DDoS Protection.

Além disso, a Unidade de Crimes Digitais da Microsoft (DCU) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controlo para ataques DDoS.

## <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos Azure

É imperativo entender o alcance do seu risco de um ataque DDoS numa base contínua. Pergunte-se periodicamente:

- Que novos recursos azure publicamente disponíveis precisam de proteção?

- Há um único ponto de falha no serviço? 

- Como é que os serviços podem ser isolados para limitar o impacto de um ataque, ao mesmo tempo que disponibilizam serviços a clientes válidos?

- Existem redes virtuais onde o DDoS Protection Standard deve ser ativado mas não é? 

- Os meus serviços estão ativos/ativos com falhas em várias regiões?

É essencial que compreenda o comportamento normal de uma aplicação e se prepare para agir se a aplicação não se comportar como esperado durante um ataque DDoS. Tenha monitores configurados para as suas aplicações críticas ao negócio que imitam o comportamento do cliente e notifique-o quando forem detetadas anomalias relevantes. Consulte as [melhores práticas de monitorização e diagnóstico](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) para obter informações sobre a saúde da sua aplicação.

[A azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço extensível de gestão de desempenho de aplicações (APM) para desenvolvedores web em várias plataformas. Utilize insights de aplicação para monitorizar a sua aplicação web ao vivo. Deteta automaticamente anomalias de desempenho. Inclui ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="customer-ddos-response-team"></a>Equipa de resposta DDoS do cliente

Criar uma equipa de resposta do DDoS é um passo fundamental para responder a um ataque de forma rápida e eficaz. Identifique os contactos na sua organização que supervisionarão o planeamento e a execução. Esta equipa de resposta DDoS deve compreender cuidadosamente o serviço Azure DDoS Protection Standard. Certifique-se de que a equipa pode identificar e mitigar um ataque coordenando com clientes internos e externos, incluindo a equipa de suporte da Microsoft. 

Recomendamos que utilize exercícios de simulação como parte normal da disponibilidade do seu serviço e planeamento de continuidade, e estes exercícios devem incluir testes de escala. Consulte [o teste através de simulações](test-through-simulations.md) para aprender a simular o tráfego de teste DDoS contra os seus pontos finais públicos Azure.

## <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A Azure DDoS Protection Standard identifica e atenua os ataques DDoS sem qualquer intervenção do utilizador. Para ser notificado quando há uma mitigação ativa para um IP público protegido, você pode [configurar alertas](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Quando entrar em contato com o suporte da Microsoft

Os clientes Azure DDoS Protection Standard têm acesso à equipa DDoS Rapid Response (DRR), que pode ajudar na investigação de ataque durante um ataque, bem como na análise pós-ataque. Consulte [a DDoS Rapid Response](ddos-rapid-response.md) para obter mais detalhes, incluindo quando deve envolver a equipa de DRR.

## <a name="post-attack-steps"></a>Passos pós-ataque

É sempre uma boa estratégia fazer uma autópsia após um ataque e ajustar a estratégia de resposta do DDoS conforme necessário. Coisas a considerar:

- Houve alguma perturbação no serviço ou na experiência do utilizador devido à falta de arquitetura escalável?

- Quais aplicações ou serviços foram os que mais sofreram?

- Quão eficaz foi a estratégia de resposta do DDoS e como pode ser melhorada?

Se suspeita que está sob um ataque DDoS, aumente pelos seus canais normais de apoio ao Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).