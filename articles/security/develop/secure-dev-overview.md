---
title: Garantir as melhores práticas de desenvolvimento no Microsoft Azure
description: As melhores práticas para ajudá-lo a desenvolver um código mais seguro e implementar uma aplicação mais segura na nuvem.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517177"
---
# <a name="secure-development-best-practices-on-azure"></a>Garantir as melhores práticas de desenvolvimento em Azure
Esta série de artigos apresenta atividades de segurança e controlos a ter em conta quando desenvolve aplicações para a nuvem. As fases do Ciclo de Vida para o Desenvolvimento da Segurança da Microsoft (SDL) e questões e conceitos de segurança a ter em conta durante cada fase do ciclo de vida são abrangidas. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar em cada fase do ciclo de vida para projetar, desenvolver e implementar uma aplicação mais segura.

As recomendações nos artigos vêm da nossa experiência com a segurança da Azure e das experiências dos nossos clientes. Pode usar estes artigos como referência para o que deve considerar durante uma fase específica do seu projeto de desenvolvimento, mas sugerimos que também leia todos os artigos do início ao fim pelo menos uma vez. Ler todos os artigos apresenta-lhe conceitos que pode ter perdido em fases anteriores do seu projeto. Implementar estes conceitos antes de lançar o seu produto pode ajudá-lo a construir software seguro, abordar os requisitos de conformidade de segurança e reduzir os custos de desenvolvimento.

Estes artigos destinam-se a ser um recurso para designers de software, desenvolvedores e testadores a todos os níveis que constroem e implementam aplicações seguras do Azure.

## <a name="overview"></a>Descrição Geral

A segurança é um dos aspetos mais importantes de qualquer aplicação, e não é uma coisa simples de acertar. Felizmente, o Azure fornece muitos serviços que podem ajudá-lo a garantir a sua aplicação na nuvem. Estes artigos abordam atividades e serviços Azure que pode implementar em cada fase do seu ciclo de vida de desenvolvimento de software para ajudá-lo a desenvolver um código mais seguro e implementar uma aplicação mais segura na nuvem.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguir as melhores práticas para o desenvolvimento seguro de software requer a integração da segurança em cada fase do ciclo de vida de desenvolvimento de software, desde a análise de requisitos à manutenção, independentemente da metodologia do projeto ([cascata,](https://en.wikipedia.org/wiki/Waterfall_model) [ágil,](https://en.wikipedia.org/wiki/Agile_software_development)ou [DevOps).](https://en.wikipedia.org/wiki/DevOps) Na sequência de violações de dados de alto perfil e da exploração de falhas de segurança operacional, mais desenvolvedores estão a entender que a segurança precisa de ser abordada ao longo do processo de desenvolvimento.

Quanto mais tarde corrigires um problema no teu ciclo de vida de desenvolvimento, mais essa correção te custará. Questões de segurança não são exceção. Se ignorar problemas de segurança nas fases iniciais do desenvolvimento do seu software, cada fase que se segue pode herdar as vulnerabilidades da fase anterior. O seu produto final terá acumulado múltiplos problemas de segurança e a possibilidade de uma violação. A segurança de construção em cada fase do ciclo de vida de desenvolvimento ajuda-o a apanhar problemas mais cedo, e ajuda-o a reduzir os seus custos de desenvolvimento.

Seguimos as fases do Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) para introduzir atividades e serviços Azure que pode utilizar para cumprir práticas seguras de desenvolvimento de software em cada fase do ciclo de vida.

As fases SDL são:

  - Formação
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Libertar
  - Resposta

![Ciclo de Vida de Desenvolvimento da Segurança](./media/secure-dev-overview/01-sdl-phase.png)

Nestes artigos agru nos grupos SDL em design, desenvolvimento e implantação.

## <a name="engage-your-organizations-security-team"></a>Envolva a equipa de segurança da sua organização

A sua organização pode ter um programa formal de segurança de aplicações que o ajuda com atividades de segurança do início ao fim durante o ciclo de vida do desenvolvimento. Se a sua organização tiver equipas de segurança e conformidade, não se esqueça de as envolver antes de começar a desenvolver a sua aplicação. Pergunte-lhes em cada fase do SDL se existem algumas tarefas que tenha perdido.

Entendemos que muitos leitores podem não ter uma equipa de segurança ou conformidade para se envolver. Estes artigos podem ajudá-lo a guiá-lo nas questões de segurança e decisões que deve considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Use os seguintes recursos para saber mais sobre o desenvolvimento de aplicações seguras e para ajudar a garantir as suas aplicações no Azure:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – O SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a construir software mais seguro. Ajuda-o a resolver os requisitos de conformidade com a segurança, reduzindo os custos de desenvolvimento.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP é uma comunidade online que produz artigos, metodologias, documentação, ferramentas e tecnologias gratuitamente disponíveis no domínio da segurança das aplicações web.

[PushIng Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – Uma série de artigos online que descreve diferentes tipos de atividades de segurança de aplicações que os desenvolvedores devem completar para criar um código mais seguro.

[Plataforma de identidade da Microsoft](../../active-directory/develop/index.yml) – A plataforma de identidade da Microsoft é uma evolução do serviço de identidade AD AD e plataforma de desenvolvimento. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas de código aberto, registo de aplicações e configuração, documentação completa do desenvolvedor, amostras de código e outros conteúdos do desenvolvedor. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria como OAuth 2.0 e OpenID Connect.

[Boas práticas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – Uma coleção de boas práticas de segurança para usar quando projeta, implementa e gere soluções em nuvem utilizando o Azure. Este artigo destina-se a ser um recurso para profissionais de TI. Isto pode incluir designers, arquitetos, desenvolvedores e testadores que constroem e implementam soluções seguras Azure.

[As Plantas de Segurança e Conformidade em Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure Security and Compliance Blueprints são recursos que podem ajudá-lo a construir e lançar aplicações alimentadas a nuvem que cumprem os rigorosos regulamentos e padrões.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Design de aplicações seguras](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
- [Implementar aplicações seguras](secure-deploy.md)