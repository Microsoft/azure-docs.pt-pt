---
title: Boas práticas de desenvolvimento seguros no Microsoft Azure
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
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "68934858"
---
# <a name="secure-development-best-practices-on-azure"></a>Boas práticas de desenvolvimento seguros em Azure
Esta série de artigos apresenta atividades e controlos de segurança a considerar quando desenvolve aplicações para a nuvem. As fases do Ciclo de Vida de Desenvolvimento de Segurança da Microsoft (SDL) e questões e conceitos de segurança a considerar durante cada fase do ciclo de vida são abrangidas. O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar em cada fase do ciclo de vida para projetar, desenvolver e implementar uma aplicação mais segura.

As recomendações nos artigos vêm da nossa experiência com a segurança azure e das experiências dos nossos clientes. Pode usar estes artigos como referência para o que deve considerar durante uma fase específica do seu projeto de desenvolvimento, mas sugerimos que leia também todos os artigos do início ao fim pelo menos uma vez. Ler todos os artigos apresenta-o a conceitos que pode ter perdido em fases anteriores do seu projeto. Implementar estes conceitos antes de lançar o seu produto pode ajudá-lo a construir software seguro, a responder aos requisitos de conformidade de segurança e a reduzir os custos de desenvolvimento.

Estes artigos destinam-se a ser um recurso para designers de software, desenvolvedores e testadores a todos os níveis que constroem e implementam aplicações Seguras do Azure.

## <a name="overview"></a>Descrição geral

A segurança é um dos aspetos mais importantes de qualquer aplicação, e não é uma coisa simples de acertar. Felizmente, o Azure fornece muitos serviços que podem ajudá-lo a garantir a sua aplicação na nuvem. Estes artigos abordam atividades e serviços Azure que pode implementar em cada fase do seu ciclo de vida de desenvolvimento de software para ajudá-lo a desenvolver um código mais seguro e implementar uma aplicação mais segura na nuvem.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguindo as melhores práticas para o desenvolvimento seguro de software requer integrar a segurança em cada fase do ciclo de vida de desenvolvimento de software, desde a análise de requisitos à manutenção, independentemente da metodologia do projeto[(cachoeira,](https://en.wikipedia.org/wiki/Waterfall_model) [ágil,](https://en.wikipedia.org/wiki/Agile_software_development)ou [DevOps).](https://en.wikipedia.org/wiki/DevOps) Na sequência de violações de dados de alto perfil e da exploração de falhas de segurança operacional, mais desenvolvedores estão a compreender que a segurança tem de ser abordada ao longo do processo de desenvolvimento.

Quanto mais tarde resolveres um problema no teu ciclo de vida de desenvolvimento, mais essa correção te custará. Questões de segurança não são exceção. Se ignorar os problemas de segurança nas fases iniciais do desenvolvimento do seu software, cada fase que se segue pode herdar as vulnerabilidades da fase anterior. O seu produto final terá acumulado vários problemas de segurança e a possibilidade de uma violação. Construir segurança em cada fase do ciclo de vida de desenvolvimento ajuda-o a resolver problemas mais cedo, e ajuda-o a reduzir os seus custos de desenvolvimento.

Acompanhamos as fases do Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) para introduzir atividades e serviços Azure que pode utilizar para cumprir práticas seguras de desenvolvimento de software em cada fase do ciclo de vida.

As fases SDL são:

  - Formação
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Libertar
  - Resposta

![Ciclo de Vida de Desenvolvimento da Segurança](./media/secure-dev-overview/01-sdl-phase.png)

Nestes artigos agrupamos as fases SDL em design, desenvolvimento e implantação.

## <a name="engage-your-organizations-security-team"></a>Envolva a equipa de segurança da sua organização

A sua organização pode ter um programa formal de segurança de aplicações que o ajuda com atividades de segurança do início ao fim durante o ciclo de vida do desenvolvimento. Se a sua organização tiver equipas de segurança e conformidade, certifique-se de as envolver antes de começar a desenvolver a sua aplicação. Pergunte-lhes em cada fase do SDL se há alguma tarefa que tenha perdido.

Entendemos que muitos leitores podem não ter uma equipa de segurança ou conformidade para se envolverem. Estes artigos podem ajudar a guiá-lo nas questões e decisões de segurança que você precisa considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Utilize os seguintes recursos para saber mais sobre o desenvolvimento de aplicações seguras e para ajudar a garantir as suas aplicações no Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – O SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a construir software mais seguro. Ajuda-o a responder aos requisitos de conformidade com a segurança, reduzindo os custos de desenvolvimento.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – A OWASP é uma comunidade online que produz artigos, metodologias, documentação, ferramentas e tecnologias disponíveis livremente no campo da segurança da aplicação web.

[Push Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – Uma série de artigos online que descreve diferentes tipos de atividades de segurança de aplicações que os desenvolvedores devem completar para criar um código mais seguro.

[Plataforma de identidade microsoft](../../active-directory/develop/index.yml) – A plataforma de identidade microsoft é uma evolução do serviço de identidade Azure AD e plataforma de desenvolvimento. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas de código aberto, registo e configuração de aplicações, documentação completa do desenvolvedor, amostras de código e outros conteúdos de desenvolvedor. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria como o OAuth 2.0 e o OpenID Connect.

[Boas práticas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – Uma coleção de boas práticas de segurança para usar quando projeta, implementa e gere soluções em nuvem utilizando o Azure. Este artigo destina-se a ser um recurso para profissionais de TI. Isto pode incluir designers, arquitetos, desenvolvedores e testadores que constroem e implementam soluções Azure seguras.

As plantas de segurança e conformidade em Plantas de Segurança e Conformidade [Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) são recursos que podem ajudá-lo a construir e lançar aplicações alimentadas por nuvem que cumprem rigorosos regulamentos e normas.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Aplicações seguras de design](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
- [Implementar aplicações seguras](secure-deploy.md)
