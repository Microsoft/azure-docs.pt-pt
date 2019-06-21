---
title: Proteger o desenvolvimento de melhores práticas no Microsoft Azure
description: Melhores práticas para o ajudar a desenvolver o código mais seguro e implementar uma aplicação mais segura na cloud.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144467"
---
# <a name="secure-development-best-practices-on-azure"></a>Proteger o desenvolvimento de melhores práticas no Azure
Esta série de artigos Apresenta atividades de segurança e controlos a serem considerados ao desenvolver aplicativos para a nuvem. As fases das perguntas de segurança e o Microsoft Security Development Lifecycle (SDL) e conceitos a serem considerados durante cada fase do ciclo de vida são abordadas. O objetivo é ajudar a definir as atividades e serviços do Azure que pode utilizar em cada fase do ciclo de vida para criar, desenvolver e implementar uma aplicação mais segura.

As recomendações nos artigos são fornecidos a nossa experiência com a segurança do Azure e a partir das experiências de nossos clientes. Pode utilizar estes artigos como uma referência para o que deve considerar durante uma fase específica do seu projeto de desenvolvimento, mas sugerimos que leia também a todos os artigos do início ao fim, pelo menos, uma vez. Ler todos os artigos apresenta conceitos que talvez não tenham ocorrido em fases anteriores do seu projeto. Implementar esses conceitos, antes de lançar seu produto pode ajudá-lo a criar softwares seguros, abordar os requisitos de conformidade de segurança e reduzir os custos de desenvolvimento.

Estes artigos se destinam a ser um recurso para designers de software, os desenvolvedores, e os testadores em todos os níveis que criar e implementar aplicações do Azure seguras.

## <a name="overview"></a>Descrição geral

A segurança é um dos aspectos mais importantes de qualquer aplicativo, e não é uma solução simples de solucionar. Felizmente, o Azure fornece vários serviços que podem ajudar a proteger a sua aplicação na cloud. Estes artigos de endereços atividades e serviços do Azure, pode implementar em cada etapa do seu ciclo de vida de desenvolvimento de software para o ajudar a desenvolver o código mais seguro e implementar uma aplicação mais segura na cloud.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguir as melhores práticas para o desenvolvimento de software seguro necessita da integração de segurança em cada fase do ciclo de vida de desenvolvimento de software, da análise de requisitos para manutenção, independentemente da metodologia de projeto ([cascata](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development), ou [DevOps](https://en.wikipedia.org/wiki/DevOps)). A reativação de violações de dados de perfil alto e a exploração de falhas de segurança operacional, mais desenvolvedores estão entender que segurança precisa ser resolvidos durante o processo de desenvolvimento.

O mais tarde corrigir um problema no seu ciclo de vida de desenvolvimento, mais isso corrigir irá custam. Problemas de segurança não são exceção. Se ignorar problemas de segurança nas fases iniciais de desenvolvimento de software, cada fase que se segue pode herdar as vulnerabilidades da fase anterior. O produto final serão acumulados vários problemas de segurança e a possibilidade de uma violação. Criação de segurança em cada fase do ciclo de vida de desenvolvimento ajuda-o a capturar problemas antecipadamente e ajuda-o a reduzir os custos de desenvolvimento.

Seguimos as fases do Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) atividades e serviços do Azure que pode utilizar para atender às práticas de desenvolvimento de software seguro em cada fase do ciclo de vida.

As fases SDL são:

  - Formação
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Libertar
  - Resposta

![Ciclo de vida de desenvolvimento de segurança](./media/secure-dev-overview/01-sdl-phase.png)

Estes artigos, agrupar as fases do SDL em design, desenvolver e implementar.

## <a name="engage-your-organizations-security-team"></a>Envolver a equipe de segurança da sua organização

Sua organização pode ter um programa de segurança de aplicativo formal que ajuda a com atividades de segurança do início ao fim durante o ciclo de vida de desenvolvimento. Se sua organização tiver as equipes de segurança e conformidade, certifique-se de que contactá-lo antes de começar a desenvolver a sua aplicação. Peça-lhes em cada fase do SDL se existem quaisquer tarefas que tenha perdido.

Estamos cientes de que muitos leitores podem não ter uma equipe de segurança ou conformidade de firmar compromissos. Estes artigos podem ajudar a orientá-lo nas perguntas de segurança e as decisões que precisa considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Utilize os seguintes recursos para saber mais sobre o desenvolvimento de aplicativos seguros e para ajudar a proteger as suas aplicações no Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – o SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a criarem softwares mais seguros. Ajuda-o a abordar os requisitos de conformidade de segurança, reduzindo os custos de desenvolvimento.

[Abra o Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP é uma comunidade online que produz artigos disponíveis gratuitamente, metodologias, documentação, ferramentas e tecnologias no campo da segurança de aplicativos web.

[Enviar por push à esquerda, como o chefe](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – uma série de artigos online que descreve os diferentes tipos de atividades de segurança de aplicação que os desenvolvedores devem ser concluídas para criar código mais seguro.

[Plataforma de identidade do Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) – plataforma de identidade da Microsoft é uma evolução da plataforma de serviço e desenvolvedor de identidade do Azure AD. É uma plataforma completa que consiste num serviço de autenticação, bibliotecas de código-fonte aberto, registo de aplicação e configuração, documentação de programador completa, exemplos de código e outros conteúdos programador. A plataforma de identidade da Microsoft oferece suporte a protocolos de norma da indústria, como o OAuth 2.0 e OpenID Connect.

[Melhores práticas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – uma coleção de melhores práticas de segurança a utilizar ao conceber, implementar e gerir soluções na cloud ao utilizar o Azure. Este documento destina-se para ser um recurso para profissionais de TI. Isso pode incluir designers, arquitetos, desenvolvedores e testadores que criar e implementar soluções do Azure seguras.

[Segurança e esquemas de conformidade no Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – segurança do Azure e esquemas de conformidade são recursos que podem ajudá-lo a criar e iniciar as aplicações com tecnologia de cloud que cumpram as normas e regulamentos rigorosos.

## <a name="next-steps"></a>Passos Seguintes
Nos seguintes artigos, recomendamos que os controlos de segurança e atividades que podem ajudá-lo a criar, desenvolver e implantar aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Desenvolver aplicações seguras](secure-develop.md)
- [Implemente aplicações seguras](secure-deploy.md)
