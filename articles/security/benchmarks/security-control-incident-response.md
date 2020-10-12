---
title: Controlo de Segurança Azure - Resposta a incidentes
description: Resposta a incidentes de controlo de segurança da Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408422"
---
# <a name="security-control-incident-response"></a>Controlo de Segurança: Resposta a incidentes

Proteja a informação da organização, bem como a sua reputação, desenvolvendo e implementando uma infraestrutura de resposta a incidentes (por exemplo, planos, funções definidas, formação, comunicações, supervisão de gestão) para rapidamente descobrir um ataque e, em seguida, efetivamente conter os danos, erradicar a presença do atacante e restaurar a integridade da rede e dos sistemas.

## <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Cliente |

Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.  

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.2 | 19.8 | Cliente |

O Centro de Segurança atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Utilizar etiquetas para organizar os seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.3 | 19 | Cliente |

Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.4 | 19.5 | Cliente |

As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10,5 | 19.6 | Cliente |

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.6 | 19 | Cliente |

Utilize a funcionalidade de Automatização de Fluxo de Trabalho no Azure Security Center para desencadear automaticamente respostas através de "Aplicações lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Passos seguintes

- Veja o próximo Controlo de Segurança: [Testes de penetração e exercícios de equipa vermelha](security-control-penetration-tests-red-team-exercises.md)