---
title: 'Bem-vindo ao aplicativo wingtips – banco de dados SQL do Azure '
description: Saiba mais sobre modelos de aluguel de banco de dados e sobre o exemplo de aplicativo SaaS wingtips para o banco de dados SQL do Azure no ambiente de nuvem.
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
ms.date: 01/25/2019
ms.openlocfilehash: 6b8cb9bed2f5fdd0b246e73c84731081d0f2e41e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691832"
---
# <a name="the-wingtip-tickets-saas-application"></a>O aplicativo Wingtip tickets SaaS

O mesmo aplicativo SaaS *Wingtip tickets* é implementado em cada uma das três amostras. O aplicativo é uma lista de eventos simples e o aplicativo SaaS de tíquetes direcionando pequenos locais – teatros, paus, etc. Cada local é um locatário do aplicativo e tem seus próprios dados: detalhes do local, listas de eventos, clientes, pedidos de tíquetes, etc.  O aplicativo, junto com os scripts e tutoriais de gerenciamento, apresenta um cenário de SaaS de ponta a ponta. Isso inclui o provisionamento de locatários, monitoramento e gerenciamento de desempenho, gerenciamento de esquema e relatórios e análises entre locatários.

## <a name="three-saas-application-and-tenancy-patterns"></a>Três padrões de aplicativo e locatário de SaaS

Três versões do aplicativo estão disponíveis; cada uma explora um padrão de locação de banco de dados diferente no banco de dados SQL do Azure.  O primeiro usa um aplicativo autônomo por locatário com seu próprio banco de dados. O segundo usa um aplicativo multilocatário com um banco de dados por locatário. O terceiro exemplo usa um aplicativo multilocatário com bancos de dados de vários locatários fragmentados.

![Três padrões de locação][image-three-tenancy-patterns]

 Cada exemplo inclui o código do aplicativo, além de scripts de gerenciamento e tutoriais que exploram uma variedade de padrões de design e de gerenciamento.  Cada exemplo é implantado em menos de cinco minutos.  Todos os três podem ser implantados lado a lado para que você possa comparar as diferenças no design e no gerenciamento.

## <a name="standalone-application-per-tenant-pattern"></a>Aplicativo autônomo por padrão de locatário

O padrão aplicativo autônomo por locatário usa um aplicativo de locatário único com um banco de dados para cada locatário. O aplicativo de cada locatário, incluindo seu banco de dados, é implantado em um grupo de recursos do Azure separado. O grupo de recursos pode ser implantado na assinatura do provedor de serviços ou na assinatura do locatário e gerenciado pelo provedor em nome do locatário. O padrão aplicativo autônomo por locatário fornece o maior isolamento de locatário, mas normalmente é o mais caro, pois não há oportunidade de compartilhar recursos entre vários locatários.  Esse padrão é adequado para aplicativos que podem ser mais complexos e que são implantados em números menores de locatários.  Com implantações autônomas, o aplicativo pode ser personalizado para cada locatário com mais facilidade do que em outros padrões.  

Confira os [tutoriais][docs-tutorials-for-wingtip-sa] e o código no GitHub [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Padrão de banco de dados por locatário

O padrão de banco de dados por locatário é eficaz para provedores de serviço que se preocupam com o isolamento de locatários e desejam executar um serviço centralizado que permite o uso econômico de recursos compartilhados. Um banco de dados é criado para cada local, ou locatário, e todos os bancos são gerenciados centralmente. Os bancos de dados podem ser hospedados em pools elásticos para fornecer um gerenciamento de desempenho econômico e fácil, que aproveita os padrões de carga de trabalho imprevisíveis dos locatários. Um banco de dados de catálogo contém o mapeamento entre locatários e seus bancos de dados. Esse mapeamento é gerenciado usando os recursos de gerenciamento de mapa de fragmentos da [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md), que fornece gerenciamento de conexão eficiente para o aplicativo.

Confira os [tutoriais][docs-tutorials-for-wingtip-dpt] e o código no GitHub [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Padrão de banco de dados multilocatário fragmentado

Bancos de dados multilocatários são eficazes para provedores de serviço que procuram custos menores por locatário e que têm um isolamento de locatário reduzido. Esse padrão permite empacotar grandes números de locatários em um banco de dados individual, aumentando o custo por locatário. A escala quase infinita é possível com a fragmentação dos locatários em vários bancos de dados. Um banco de dados de catálogo mapeia locatários para bancos de dados.  

Esse padrão também permite um modelo *híbrido* no qual você pode otimizar o custo com vários locatários em um banco de dados ou otimizar o isolamento com um único locatário em seu próprio banco de dados. A escolha pode ser feita de acordo com o locatário, seja quando o locatário é provisionado ou mais tarde, sem impacto no aplicativo.  Esse modelo pode ser usado com eficiência quando grupos de locatários precisam ser tratados de forma diferente. Por exemplo, os locatários de baixo custo podem ser atribuídos a bancos de dados compartilhados, enquanto os locatários Premium podem ser atribuídos a seus próprios bancos de dados. 

Confira os [tutoriais][docs-tutorials-for-wingtip-mt] e o código no GitHub [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Passos seguintes

#### <a name="conceptual-descriptions"></a>Descrições conceituais

- Uma explicação mais detalhada dos padrões de locação de aplicativos está disponível em [padrões de locação de banco de dados SaaS multilocatário][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriais e código

- Aplicativo autônomo por locatário:
    - [Tutoriais para aplicativo autônomo][docs-tutorials-for-wingtip-sa].
    - [Código para aplicativo autônomo, no GitHub][github-code-for-wingtip-sa].

- Banco de dados por locatário:
    - [Tutoriais do banco de dados por locatário][docs-tutorials-for-wingtip-dpt].
    - [Código para banco de dados por locatário, no GitHub][github-code-for-wingtip-dpt].

- Multilocatário fragmentado:
    - [Tutoriais para multilocatário fragmentado][docs-tutorials-for-wingtip-mt].
    - [Código para multilocatário fragmentado no GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Três padrões de locação."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

