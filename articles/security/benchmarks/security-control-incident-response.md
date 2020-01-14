---
title: Controle de segurança do Azure-resposta a incidentes
description: Resposta a incidentes de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934406"
---
# <a name="security-control-incident-response"></a>Controle de segurança: resposta a incidentes

Proteja as informações da organização, bem como sua reputação, desenvolvendo e implementando uma infraestrutura de resposta a incidentes (por exemplo, planos, funções definidas, treinamento, comunicações, supervisão de gerenciamento) para descobrir rapidamente um ataque e, em seguida, que contém efetivamente o dano, erradicando a presença do invasor e restaurando a integridade da rede e dos sistemas.

## <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Cliente |

Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

Como configurar Automaçãos de fluxo de trabalho na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.2 | 19,8 | Cliente |

A central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

## <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,3 | 19 | Cliente |

Conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

Consulte a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,4 | 19,5 | Cliente |

As informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato da segurança da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.5 | 19,6 | Cliente |

Exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas sentinela.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,6 | 19 | Cliente |

Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de aplicativos lógicos &quot;&quot; em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os aplicativos lógicos:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Passos seguintes

Veja o próximo controle de segurança: [testes de penetração e exercícios de equipe vermelhos](security-control-penetration-tests-red-team-exercises.md)