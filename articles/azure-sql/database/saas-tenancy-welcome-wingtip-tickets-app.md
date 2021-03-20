---
title: Bem-vindo à app Wingtips
description: Saiba mais sobre os modelos de arrendamento de bases de dados, e sobre a aplicação SaaS da amostra Wingtips, para Azure SQL Database no ambiente de nuvem.
keywords: tutorial de base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780381"
---
# <a name="the-wingtip-tickets-saas-application"></a>A aplicação Wingtip Tickets SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A mesma aplicação *Wingtip Tickets* SaaS é implementada em cada uma das três amostras. A aplicação é uma simples listagem de eventos e bilhetes App SaaS direcionado para pequenos locais - teatros, clubes, etc. Cada local é inquilino da app, e tem os seus próprios dados: detalhes do local, listas de eventos, clientes, encomendas de bilhetes, etc.  A aplicação, juntamente com os scripts de gestão e tutoriais, apresenta um cenário SaaS de ponta a ponta. Isto inclui o a provisionamento dos inquilinos, monitorização e gestão do desempenho, gestão de esquemas e relatórios e análises de inquilinos cruzados.

## <a name="three-saas-application-and-tenancy-patterns"></a>Três padrões de aplicação saaS e de arrendamento

Estão disponíveis três versões da app; cada um explora um padrão de arrendamento de base de dados diferente na Base de Dados Azure SQL.  O primeiro utiliza uma aplicação autónoma por inquilino com base de dados própria. O segundo usa uma app multi-inquilino com uma base de dados por inquilino. A terceira amostra usa uma aplicação multi-inquilino com bases de dados de vários inquilinos.

![Três padrões de arrendamento][image-three-tenancy-patterns]

 Cada amostra inclui o código de aplicação, além de scripts de gestão e tutoriais que exploram uma gama de padrões de design e gestão.  Cada amostra é lançada em menos de cinco minutos.  Os três podem ser implantados lado a lado para que possa comparar as diferenças de design e gestão.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicação autónoma por padrão de inquilino

A app autónoma por padrão de inquilino usa uma aplicação de inquilino único com uma base de dados para cada inquilino. A aplicação de cada inquilino, incluindo a sua base de dados, é implantada num grupo de recursos Azure separado. O grupo de recursos pode ser implantado na assinatura do prestador de serviços ou na assinatura do arrendatário e gerido pelo prestador em nome do arrendatário. A app autónoma por padrão de inquilino proporciona o maior isolamento do inquilino, mas é tipicamente a mais cara, uma vez que não há oportunidade de compartilhar recursos entre vários inquilinos.  Este padrão é adequado para aplicações que podem ser mais complexas e que são implantadas para um número menor de inquilinos.  Com implementações autónomas, a app pode ser personalizada para cada inquilino mais facilmente do que em outros padrões.  

Confira os [tutoriais][docs-tutorials-for-wingtip-sa] e código no GitHub  [.../Microsoft/WingtipTicketsSaA-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Base de dados por padrão de inquilino

A base de dados por padrão de arrendamento é eficaz para os prestadores de serviços que se preocupam com o isolamento dos inquilinos e querem executar um serviço centralizado que permita uma utilização rentável de recursos partilhados. Uma base de dados é criada para cada local, ou inquilino, e todas as bases de dados são geridas centralmente. As bases de dados podem ser hospedadas em piscinas elásticas para proporcionar uma gestão de desempenho eficiente e fácil, o que alavanca os padrões de carga de trabalho imprevisíveis dos inquilinos. Uma base de dados de catálogo contém o mapeamento entre os inquilinos e as suas bases de dados. Este mapeamento é gerido utilizando as funcionalidades de gestão de mapas de fragmentos da Biblioteca de Clientes de Base de [Dados Elástica,](elastic-database-client-library.md)que proporciona uma gestão eficiente da ligação à aplicação.

Confira os [tutoriais][docs-tutorials-for-wingtip-dpt] e o código no GitHub  [.../Microsoft/WingtipTicketsSaAS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Padrão de base de dados de vários inquilinos desfasado

As bases de dados multi-inquilinos são eficazes para prestadores de serviços que procuram um custo mais baixo por inquilino e tudo bem com o isolamento reduzido dos inquilinos. Este padrão permite embalar um grande número de inquilinos em uma base de dados individual, levando o custo-por-inquilino para baixo. A escala quase infinita é possível ao escadação dos inquilinos através de várias bases de dados. Uma base de dados de catálogos mapeia inquilinos para bases de dados.  

Este padrão também permite um modelo *híbrido* no qual você pode otimizar o custo com vários inquilinos em uma base de dados, ou otimizar para o isolamento com um único inquilino na sua própria base de dados. A escolha pode ser feita numa base arrendatário,seja quando o arrendatário é a provisionado ou posteriormente, sem qualquer impacto no pedido.  Este modelo pode ser utilizado eficazmente quando grupos de inquilinos precisam de ser tratados de forma diferente. Por exemplo, os inquilinos de baixo custo podem ser designados para bases de dados partilhadas, enquanto os inquilinos premium podem ser atribuídos às suas próprias bases de dados. 

Confira os [tutoriais][docs-tutorials-for-wingtip-mt] e o código no GitHub  [.../Microsoft/WingtipTicketsSaA-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Passos seguintes

#### <a name="conceptual-descriptions"></a>Descrições conceptuais

- Uma explicação mais detalhada dos padrões de arrendamento da aplicação está disponível em [padrões de arrendamento de base de dados SaaS multi-inquilinos][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriais e código

- App autónoma por inquilino:
    - [Tutoriais para aplicação autónoma.][docs-tutorials-for-wingtip-sa]
    - [Código para aplicação autónoma, no GitHub.][github-code-for-wingtip-sa]

- Base de dados por inquilino:
    - [Tutoriais para base de dados por inquilino.][docs-tutorials-for-wingtip-dpt]
    - [Código para base de dados por inquilino, no GitHub.][github-code-for-wingtip-dpt]

- Multi-inquilino desotado:
    - [Tutoriais para multi-inquilinos.][docs-tutorials-for-wingtip-mt]
    - [Código para multi-inquilinos, no GitHub.][github-code-for-wingtip-mt]



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Três padrões de arrendamento."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb