---
title: Controlo de Segurança Azure - Resposta a Incidentes
description: Resposta a incidentes de controlo de segurança azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408422"
---
# <a name="security-control-incident-response"></a>Controlo de Segurança: Resposta a Incidentes

Proteja a informação da organização, bem como a sua reputação, desenvolvendo e implementando uma infraestrutura de resposta a incidentes (por exemplo, planos, papéis definidos, formação, comunicações, supervisão de gestão) para descobrir rapidamente um ataque e, em seguida, efetivamente conter os danos, erradicar a presença do atacante, e restaurar a integridade da rede e sistemas.

## <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Cliente |

Construa um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.  

- [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveite o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.2 | 19.8 | Cliente |

O Centro de Segurança atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (para ex. produção, não prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Use tags para organizar os seus recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.3 | 19 | Cliente |

Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - Guia para Programas de Teste, Formação e Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.4 | 19.5 | Cliente |

As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10,5 | 19.6 | Cliente |

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos do Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.6 | 19 | Cliente |

Utilize a funcionalidade workflow Automation no Azure Security Center para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Passos seguintes

- Veja o próximo Controlo de Segurança: [Testes de Penetração e Exercícios de Equipa Vermelha](security-control-penetration-tests-red-team-exercises.md)