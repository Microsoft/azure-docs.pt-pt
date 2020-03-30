---
title: Implementar aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de lançamento e resposta do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934873"
---
# <a name="deploy-secure-applications-on-azure"></a>Implementar aplicações seguras no Azure
Neste artigo apresentamos atividades e controlos de segurança a considerar quando implementa aplicações para a nuvem. Questões e conceitos de segurança a considerar durante as fases de lançamento e resposta do Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para implementar uma aplicação mais segura.

As seguintes fases SDL são abrangidas neste artigo:

- Libertar
- Resposta

## <a name="release"></a>Libertar
O foco da fase de lançamento está a preparar um projeto de lançamento público.
Isto inclui planear formas de executar eficazmente tarefas de manutenção pós-lançamento e abordar vulnerabilidades de segurança que possam ocorrer mais tarde.

### <a name="check-your-applications-performance-before-you-launch"></a>Verifique o desempenho da sua aplicação antes do lançamento

Verifique o desempenho da sua aplicação antes de lançar ou implementar atualizações para produção. Faça testes de [carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) baseados na nuvem utilizando o Visual Studio para encontrar problemas de desempenho na sua aplicação, melhorar a qualidade de implementação, certificar-se de que a sua aplicação está sempre disponível ou disponível, e que a sua aplicação pode lidar com o tráfego para o seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicação web

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Entre estas explorações estão ataques de injeção SQL e ataques de scripts transversais. Prevenir estes ataques no código de aplicação pode ser um desafio. Pode requerer uma manutenção rigorosa, remendos e monitorização em muitas camadas da topologia da aplicação. Uma WAF centralizada ajuda a simplificar a gestão da segurança. Uma solução WAF também pode reagir a uma ameaça de segurança, remendando uma vulnerabilidade conhecida num local central versus garantir cada aplicação web individual.

O Portal de [Aplicações Azure WAF](../../application-gateway/waf-overview.md) fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. O WAF baseia-se em regras da regra central da [OWASP, fixa-se](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para ajudá-lo a lidar com novas ameaças que podem surgir ao longo do tempo. A elaboração de um plano de resposta a incidentes inclui identificar contactos de emergência de segurança apropriados e estabelecer planos de manutenção de segurança para código que é herdado de outros grupos da organização e para código de terceiros licenciado.

### <a name="conduct-a-final-security-review"></a>Realizar uma revisão final de segurança

Rever deliberadamente todas as atividades de segurança que foram realizadas ajuda a garantir a prontidão para a libertação ou aplicação do seu software. A revisão final de segurança (RSeF) inclui geralmente a análise de modelos de ameaça, saídas de ferramentas e desempenho contra os portões de qualidade e barras de bugs que foram definidas na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar a libertação e o arquivo

Certificar o software antes de uma libertação ajuda a garantir que os requisitos de segurança e privacidade são cumpridos. Arquivar todos os dados pertinentes é essencial para a realização de tarefas de manutenção pós-lançamento. O arquivamento também ajuda a reduzir os custos a longo prazo associados à engenharia de software sustentado.

## <a name="response"></a>Resposta
A fase pós-lançamento de resposta centra-se na capacidade e disponibilização da equipa de desenvolvimento para responder adequadamente a quaisquer relatos de ameaças e vulnerabilidades de software emergentes.

### <a name="execute-the-incident-response-plan"></a>Execute o plano de resposta ao incidente

Ser capaz de implementar o plano de resposta a incidentes instituído na fase de lançamento é essencial para ajudar a proteger os clientes de vulnerabilidades de segurança de software ou privacidade que surjam.

### <a name="monitor-application-performance"></a>Monitorizar o desempenho da aplicação

A monitorização contínua da sua aplicação depois de implementada potencialmente ajuda-o a detetar problemas de desempenho, bem como vulnerabilidades de segurança.
Os serviços Azure que ajudam na monitorização da aplicação são:

  - Azure Application Insights
  - Centro de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Os Insights de Aplicação detetam automaticamente anomalias de desempenho. Inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e entender o que os utilizadores realmente fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Azure Security Center](../../security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade na (e controlo sobre) a segurança dos seus recursos Azure, incluindo aplicações web. O Centro de Segurança Azure ajuda a detetar ameaças que de outra forma podem passar despercebidas. Funciona com várias soluções de segurança.

O nível gratuito do Security Center oferece segurança limitada apenas para os seus recursos Azure. O [nível Standard](../../security-center/security-center-onboarding.md) do Centro de Segurança alarga estas capacidades a recursos no local e outras nuvens.
O Padrão do Centro de Segurança ajuda-o:

  - Encontrar e corrigir vulnerabilidades de segurança.
  - Aplicar controlos de acesso e aplicação para bloquear atividades maliciosas.
  - Detete ameaças usando analítica e inteligência.
  - Responda rapidamente quando estiver sob ataque.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a projetar e desenvolver aplicações seguras.

- [Aplicações seguras de design](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
