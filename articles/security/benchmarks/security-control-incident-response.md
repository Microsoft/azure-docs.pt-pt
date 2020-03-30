---
title: Controlo de Segurança Azure - Resposta a Incidentes
description: Resposta a incidentes de controlo de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934406"
---
# <a name="security-control-incident-response"></a>Controlo de Segurança: Resposta a Incidentes

Proteja a informação da organização, bem como a sua reputação, desenvolvendo e implementando uma infraestrutura de resposta a incidentes (por exemplo, planos, papéis definidos, formação, comunicações, supervisão de gestão) para descobrir rapidamente um ataque e, em seguida, efetivamente contendo os danos, erradicando a presença do atacante, e restaurando a integridade da rede e dos sistemas.

## <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Cliente |

Construa um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.2 | 19.8 | Cliente |

O Centro de Segurança atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.

## <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.3 | 19 | Cliente |

Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.4 | 19.5 | Cliente |

As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10,5 | 19.6 | Cliente |

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para O Sentinela Azul:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.6 | 19 | Cliente |

Utilize a funcionalidade workflow Automation no Azure Security &quot;Center&quot; para ativar automaticamente respostas através de Aplicações Lógicas em alertas de segurança e recomendações.

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Passos seguintes

Veja o próximo controlo de segurança: [Testes de penetração e exercícios](security-control-penetration-tests-red-team-exercises.md) de equipa vermelha