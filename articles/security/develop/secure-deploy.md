---
title: Implemente aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de lançamento e de resposta do seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144454"
---
# <a name="deploy-secure-applications-on-azure"></a>Implemente aplicações seguras no Azure
Neste artigo, apresentamos as atividades de segurança e controlos a ter em consideração ao implementar aplicações para a nuvem. Perguntas de segurança e conceitos a serem considerados durante as fases de lançamento e de resposta da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudar a definir as atividades e serviços do Azure que pode utilizar para implementar uma aplicação mais segura.

As seguintes fases SDL são abordadas neste artigo:

- Libertar
- Resposta

## <a name="release"></a>Libertar
O enfoque da fase de lançamento está preparando um projeto para lançamento público.
Isto inclui o planeamento de formas para efetivamente executar tarefas de manutenção de pós-lançamento e resolver vulnerabilidades de segurança que possam ocorrer mais tarde.

### <a name="check-your-applications-performance-before-you-launch"></a>Verificar o desempenho do aplicativo antes de iniciar

Verifique o desempenho do aplicativo antes de iniciá-lo ou implementar atualizações em produção. Executar com base na cloud [testes de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) usando o Visual Studio para encontrar problemas de desempenho na sua aplicação, melhorar a qualidade de implementação, certifique-se de que seu aplicativo esteja sempre disponível ou se, e que seu aplicativo pode manipular tráfego para o seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicações web

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Ataques de injeção de SQL e ataques de script entre sites, são comuns entre essas explorações. Impedir esses ataques no código da aplicação pode ser um desafio. Poderá precisar de manutenção rigorosa, a aplicação de patches e a monitorização em muitas camadas da topologia da aplicação. Uma WAF centralizada ajuda a simplificar a gestão de segurança. Uma solução WAF também pode reagir a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central em vez de proteger cada aplicativo web individualmente.

O [WAF do Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/waf-overview) fornece proteção centralizada das suas aplicações web de exploits e vulnerabilidades comuns. O WAF baseia-se nas regras da [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para o ajudar a resolver as ameaças novos que podem surgir ao longo do tempo. Preparar um plano de resposta a incidentes inclui a identificação de contactos de emergência de segurança adequados e estabelecendo uma segurança com o código que é herdado de outros grupos da organização e para o código de terceiros licenciado de planos de manutenção.

### <a name="conduct-a-final-security-review"></a>Conduzir uma revisão de segurança final

Rever deliberadamente todas as atividades de segurança que foram efetuadas ajuda a garantir a preparação para a sua versão de software ou a aplicação. A revisão final de segurança (FSR) normalmente inclui a examinar os modelos de ameaças, saídas de ferramentas e desempenho em relação a limites de qualidade e as barras de bugs que foram definidas na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar a versão e de arquivo

Certificação de software antes de uma versão ajuda a garantir o cumprimento de requisitos de segurança e privacidade. Arquivamento de todos os dados pertinentes é essencial para realizar tarefas de manutenção de pós-lançamento. Arquivamento também ajuda a reduzir os custos de longo prazo associados com constante de engenharia de software.

## <a name="response"></a>Resposta
A fase de pós-versão resposta centra-se a equipe de desenvolvimento que está a ser possível e está disponível para responder adequadamente a todos os relatórios de vulnerabilidades e ameaças emergentes de software.

### <a name="execute-the-incident-response-plan"></a>Executar o plano de resposta a incidentes

A capacidade de implementar o plano de resposta a incidentes instituiu na fase de lançamento é essencial para ajudar a proteger os clientes contra vulnerabilidades de segurança ou privacidade de software que surgem.

### <a name="monitor-application-performance"></a>Monitorizar o desempenho da aplicação

Monitorização contínua do seu aplicativo após sua implantação potencialmente ajuda-o a detetar problemas de desempenho, bem como vulnerabilidades de segurança.
Serviços do Azure que auxiliá-monitorização de aplicações são:

  - Azure Application Insights
  - Centro de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

[O Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. O Application Insights Deteta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise poderosas para ajudar a diagnosticar problemas e compreender o que os utilizadores fazem realmente com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade (e controlo sobre) a segurança dos seus recursos do Azure, incluindo aplicações web. Centro de segurança do Azure ajuda a detetar ameaças que caso contrário podem passar despercebidas. Ele funciona com várias soluções de segurança.

O escalão gratuito do Centro de segurança oferece segurança limitada para apenas os recursos do Azure. O [escalão Standard do Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-onboarding) expande estas capacidades para recursos no local e noutras clouds.
Centro de segurança Standard ajuda-o:

  - Encontre e corrija vulnerabilidades de segurança.
  - Aplica controlos de acesso e aplicação para bloquear atividade maliciosa.
  - Detete ameaças com análise e inteligência.
  - Responda rapidamente quando sob ataque.

## <a name="next-steps"></a>Passos Seguintes
Nos seguintes artigos, recomendamos que os controlos de segurança e atividades que podem ajudá-lo a projetar e desenvolver aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
