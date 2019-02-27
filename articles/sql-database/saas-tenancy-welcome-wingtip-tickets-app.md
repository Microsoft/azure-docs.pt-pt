---
title: Bem-vindo ao Wingtips aplicação - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre os modelos de inquilinos de base de dados e sobre a aplicação de Wingtips SaaS de exemplo, para a base de dados do Azure SQL no ambiente de cloud.
keywords: tutorial de base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a21b9f45d70151c2a2c2ae8ac6328a3eeb609b29
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883339"
---
# <a name="the-wingtip-tickets-saas-application"></a>A aplicação Wingtip Tickets SaaS

O mesmo *Wingtip Tickets* aplicação SaaS é implementada em cada uma das três amostras. A aplicação é um evento simple de listagem e emissão de permissões de aplicação SaaS direcionamento locais pequenos - anfiteatros, clubes, etc. Cada local é um inquilino da aplicação e tem seus próprios dados: na localização de detalhes, listas de eventos, os clientes, pedidos de permissão, etc.  A aplicação, juntamente com os scripts de gestão e os tutoriais, apresenta um cenário de SaaS de ponto-a-ponto. Isto inclui o aprovisionamento de inquilinos, monitorizar e gerir o desempenho, gestão de esquemas e entre inquilinos relatórios e análise.

## <a name="three-saas-application-and-tenancy-patterns"></a>Três padrões de aplicativo e inquilinos de SaaS

Existem três versões da aplicação; cada explora um padrão de inquilinos de base de dados diferente na base de dados do Azure SQL.  O primeiro usa um aplicativo autônomo por inquilino com a sua própria base de dados. O segundo utiliza uma aplicação multi-inquilino com um banco de dados por inquilino. O terceiro exemplo utiliza uma aplicação multi-inquilino com bancos de dados de multi-inquilinos em partição horizontal.

![Três padrões de inquilinos][image-three-tenancy-patterns]

 Cada exemplo inclui o código da aplicação, além de scripts de gerenciamento e tutoriais que explore uma gama de padrões de conceção e gestão.  Cada exemplo é implementada em menor do que cinco minutos.  Todos os três podem ser implantada lado a lado, para que pode comparar as diferenças na conceção e gestão.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicativo autônomo, por padrão de inquilino

A aplicação autónoma por padrão de inquilino utiliza uma aplicação de inquilino único com uma base de dados para cada inquilino. Aplicação de cada inquilino, incluindo a respetiva base de dados, é implementada num grupo de recursos do Azure. O grupo de recursos pode ser implementado na subscrição do fornecedor de serviços ou de subscrição do inquilino e gerenciado pelo provedor em nome do inquilino. A aplicação autónoma por padrão de inquilino fornece o maior isolamento de inquilino, mas é normalmente mais cara porque não existe nenhuma oportunidade de compartilhar recursos entre vários inquilinos.  Este padrão é adequada para aplicativos que podem ser mais complexa e que é implementado num número menor de inquilinos.  Com o das implementações autónomas, a aplicação pode ser personalizada para cada inquilino mais facilmente do que em outros padrões.  

Veja a [tutoriais] [ docs-tutorials-for-wingtip-sa] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Base de dados por padrão de inquilino

A base de dados por padrão de inquilino está em vigor para fornecedores de serviços que estão preocupados com o isolamento de inquilino e quiser executar um serviço centralizado que permite a utilização de custo eficiente de recursos partilhados. Uma base de dados é criado para cada local ou o inquilino e todas as bases de dados são geridas centralmente. Bases de dados podem ser hospedados em conjuntos elásticos para fornecer económico e de gestão de desempenho fácil, que aproveita os padrões de carga de trabalho imprevisível dos inquilinos. Uma base de dados de catálogo contém o mapeamento entre os inquilinos e as respetivas bases de dados. Esse mapeamento é gerenciado a utilizar as funcionalidades de gestão do mapa de partições horizontais do [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md), que fornece gerenciamento de conexão eficiente para a aplicação.

Veja a [tutoriais] [ docs-tutorials-for-wingtip-dpt] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Padrão de base de dados de multi-inquilino em partição horizontal

Bases de dados do multi-inquilinos são eficazes para fornecedores de serviços à procura de custo mais baixo por inquilino e OK com o isolamento de inquilino reduzida. Este padrão permite empacotar um grande número de inquilinos para uma base de dados individual, impulsionando o custo por inquilino a para baixo. Dimensionamento infinito quase é possível por fragmentação os inquilinos em várias bases de dados. Uma base de dados do catálogo mapeia os inquilinos para bases de dados.  

Este padrão também permite que um *híbrida* modelo no qual pode otimizar o custo com vários inquilinos numa base de dados ou otimizar o isolamento com um único inquilino na sua própria base de dados. Tanto a escolha pode ser feita de forma inquilino por inquilino, quando o inquilino é aprovisionado ou posterior, sem afetar o aplicativo.  Esse modelo pode ser utilizado eficazmente quando os grupos de inquilinos têm de ser tratados de forma diferente. Por exemplo, os inquilinos de baixo custo podem ser atribuídos a bases de dados partilhados, enquanto inquilinos premium podem ser atribuídos a suas próprias bases de dados. 

Veja a [tutoriais] [ docs-tutorials-for-wingtip-mt] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Passos Seguintes

#### <a name="conceptual-descriptions"></a>Descrições conceituais

- Uma explicação mais detalhada dos padrões de inquilinos de aplicação está disponível em [padrões de inquilinos da base de dados de SaaS de multi-inquilino][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriais e código

- Aplicação autónoma e por inquilino:
    - [Tutoriais para uma aplicação autónoma][docs-tutorials-for-wingtip-sa].
    - [Código de aplicação autónoma e, no GitHub][github-code-for-wingtip-sa].

- Base de dados por inquilino:
    - [Tutoriais da base de dados por inquilino][docs-tutorials-for-wingtip-dpt].
    - [Código para a base de dados por inquilino, no GitHub][github-code-for-wingtip-dpt].

- Multi-inquilino em partição horizontal:
    - [Tutoriais para multi-inquilino em partição horizontal][docs-tutorials-for-wingtip-mt].
    - [Código para multi-inquilino em partição horizontal, no GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Três padrões de inquilinos."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

