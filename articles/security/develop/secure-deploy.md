---
title: Implementar aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a ter em conta durante as fases de lançamento e resposta do seu projeto de aplicação web.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68934873"
---
# <a name="deploy-secure-applications-on-azure"></a>Implementar aplicações seguras no Azure
Neste artigo apresentamos atividades de segurança e controlos a ter em conta quando implementa aplicações para a nuvem. Questões e conceitos de segurança a ter em conta durante as fases de lançamento e resposta do Ciclo de Vida para o Desenvolvimento de Segurança da Microsoft [(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são cobertos. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para implementar uma aplicação mais segura.

Neste artigo, são abrangidas as seguintes fases SDL:

- Libertar
- Resposta

## <a name="release"></a>Libertar
O foco da fase de lançamento é preparar um projeto para lançamento público.
Isto inclui planear formas de executar eficazmente tarefas de manutenção pós-lançamento e resolver vulnerabilidades de segurança que podem ocorrer mais tarde.

### <a name="check-your-applications-performance-before-you-launch"></a>Verifique o desempenho da sua aplicação antes do lançamento

Verifique o desempenho da sua aplicação antes de a lançar ou implemente atualizações para a produção. Faça testes de [carga baseados](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) na nuvem utilizando o Visual Studio para encontrar problemas de desempenho na sua aplicação, melhorar a qualidade de implementação, certificar-se de que a sua aplicação está sempre disponível ou disponível, e que a sua aplicação pode lidar com o tráfego para o seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicação web

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Comuns entre estas explorações estão ataques de injeção DE SQL e ataques de scripts em locais cruzados. Prevenir estes ataques no código de aplicação pode ser um desafio. Pode exigir uma manutenção rigorosa, remendação e monitorização em muitas camadas da topologia da aplicação. Um WAF centralizado ajuda a simplificar a gestão de segurança. Uma solução WAF também pode reagir a uma ameaça de segurança remendando uma vulnerabilidade conhecida num local central versus garantir cada aplicação web individual.

O [Azure Application Gateway WAF](../../application-gateway/waf-overview.md) fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. A WAF baseia-se nas regras da regra principal da [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para ajudá-lo a lidar com novas ameaças que podem surgir ao longo do tempo. A preparação de um plano de resposta a incidentes inclui identificar os contactos de emergência de segurança adequados e estabelecer planos de manutenção de segurança para código que é herdado de outros grupos na organização e para código de terceiros licenciado.

### <a name="conduct-a-final-security-review"></a>Realizar uma revisão final de segurança

Rever deliberadamente todas as atividades de segurança que foram realizadas ajuda a garantir a prontidão para a sua versão ou aplicação de software. A revisão final de segurança (FSR) geralmente inclui examinar modelos de ameaças, saídas de ferramentas e desempenho contra os portões de qualidade e barras de insetos que foram definidos na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar a libertação e arquivo

Certificar o software antes de uma versão ajuda a garantir que os requisitos de segurança e privacidade são cumpridos. Arquivar todos os dados pertinentes é essencial para a realização de tarefas de manutenção pós-lançamento. O arquivamento também ajuda a reduzir os custos a longo prazo associados à engenharia de software sustentado.

## <a name="response"></a>Resposta
A fase de resposta pós-lançamento centra-se na formação e disponíveis para responder adequadamente a quaisquer relatos de ameaças e vulnerabilidades emergentes do software.

### <a name="execute-the-incident-response-plan"></a>Execute o plano de resposta a incidentes

Ser capaz de implementar o plano de resposta a incidentes instituída na fase de lançamento é essencial para ajudar a proteger os clientes de vulnerabilidades de segurança de software ou privacidade que emergem.

### <a name="monitor-application-performance"></a>Monitorizar o desempenho da aplicação

A monitorização contínua da sua aplicação após a sua implementação pode ajudá-lo a detetar problemas de desempenho, bem como vulnerabilidades de segurança.
Os serviços Azure que ajudam na monitorização de aplicações são:

  - Azure Application Insights
  - Centro de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. O Application Insights deteta automaticamente anomalias de desempenho. Inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e entender o que os utilizadores realmente fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Azure Security Center](../../security-center/security-center-intro.md) ajuda a prevenir, detetar e responder a ameaças com maior visibilidade para (e controlar) a segurança dos seus recursos Azure, incluindo aplicações web. O Centro de Segurança Azure ajuda a detetar ameaças que de outra forma poderiam passar despercebidas. Funciona com várias soluções de segurança.

O nível free do Security Center oferece uma segurança limitada apenas para os seus recursos Azure. O [nível Standard do Centro de Segurança](../../security-center/security-center-onboarding.md) alarga estas capacidades a recursos no local e outras nuvens.
O Security Center Standard ajuda-o:

  - Encontrar e corrigir vulnerabilidades de segurança.
  - Aplicar controlos de acesso e aplicação para bloquear atividades maliciosas.
  - Detetar ameaças usando analítica e inteligência.
  - Responda rapidamente quando estiver sob ataque.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a projetar e desenvolver aplicações seguras.

- [Design de aplicações seguras](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
