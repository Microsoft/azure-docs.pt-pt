---
title: Criar bases de dados de nuvem escaláveis
description: Construa aplicativos de base de dados .NET escaláveis com a biblioteca de clientes Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84050227"
---
# <a name="building-scalable-cloud-databases"></a>Criar bases de dados de nuvem escaláveis
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A escala de bases de dados pode ser facilmente realizada usando ferramentas e funcionalidades escaláveis para a Base de Dados Azure SQL. Em particular, pode utilizar a biblioteca de **clientes Elastic Database** para criar e gerir bases de dados em escala. Esta funcionalidade permite-lhe desenvolver facilmente aplicações com estilhaços utilizando centenas ou mesmo milhares de bases de dados na Base de Dados Azure SQL.

Para baixar:

* A versão Java da biblioteca, ver [Repositório Central de Maven.](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools)
* A versão .NET da biblioteca, ver [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentação

1. [Introdução às ferramentas de Base de Dados Elástica](elastic-scale-get-started.md)
2. [Características da base de dados elásticas](elastic-scale-introduction.md)
3. [Gestão de mapas de partições horizontais](elastic-scale-shard-map-management.md)
4. [Migrar as bases de dados existentes para escalar](elastic-convert-to-use-elastic-tools.md)
5. [Encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md)
6. [Consultas de vários fragmentos](elastic-scale-multishard-querying.md)
7. [Adicionar um fragmento usando ferramentas elásticas de base de dados](elastic-scale-add-a-shard.md)
8. [Aplicações multi-arrendatários com ferramentas Elásticos Database e segurança ao nível da linha](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicativos de biblioteca de clientes](elastic-scale-upgrade-client-library.md) 
10. [Visão geral das consultas elásticas](elastic-query-overview.md)
11. [Glossário de ferramentas elásticas de base de dados](elastic-scale-glossary.md)
12. [Biblioteca de clientes de base de dados elástica com enquadramento de entidade](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de clientes de base de dados elástica com Dapper](elastic-scale-working-with-dapper.md)
14. [Ferramenta de fusão dividida](elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gestor de mapas de partições horizontais](elastic-database-client-library.md) 
16. [FAQ para ferramentas de base de dados elásticas](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Capacidades do cliente

A escala de aplicações utilizando *o sharding* apresenta desafios tanto para o desenvolvedor como para o administrador. A biblioteca de clientes simplifica as tarefas de gestão fornecendo ferramentas que permitem que tanto os desenvolvedores como os administradores gerem bases de dados em escala. Num exemplo típico, há muitas bases de dados, conhecidas como "fragmentos", para gerir. Os clientes estão co-localizados na mesma base de dados, e há uma base de dados por cliente (um esquema de inquilino único). A biblioteca do cliente inclui estas funcionalidades:

- **Gestão de mapas de** fragmentos : É criada uma base de dados especial chamada "shard map manager". A gestão do mapa de fragmentos é a capacidade de uma aplicação para gerir metadados sobre os seus fragmentos. Os desenvolvedores podem usar esta funcionalidade para registar bases de dados como fragmentos, descrever mapeamentos de chaves de fragmentos individuais ou intervalos chave para essas bases de dados, e manter estes metadados à medida que o número e a composição das bases de dados evolui para refletir as mudanças de capacidade. Sem a biblioteca de clientes Elastic Database, você precisaria de passar muito tempo escrevendo o código de gestão ao implementar o sharding. Para mais detalhes, consulte [a gestão do mapa Shard.](elastic-scale-shard-map-management.md)

- **Encaminhamento dependente de dados**: Imagine um pedido que entre na aplicação. Com base no valor chave de fragmentos do pedido, a aplicação precisa determinar a base de dados correta com base no valor chave. Em seguida, abre uma ligação à base de dados para processar o pedido. O encaminhamento dependente de dados proporciona a capacidade de abrir ligações com uma única chamada fácil no mapa de fragmentos da aplicação. O encaminhamento dependente de dados foi outra área do código de infraestrutura que está agora coberta pela funcionalidade na biblioteca de clientes Elastic Database. Para mais informações, consulte [o encaminhamento dependente de Dados](elastic-scale-data-dependent-routing.md).
- **Consultas multi-fragmentos (MSQ)**: Consulta multi-fragmentos funciona quando um pedido envolve vários (ou todos) fragmentos. Uma consulta multi-fragmentos executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados dos fragmentos participantes são fundidos num conjunto de resultados globais utilizando a semântica UNION ALL. A funcionalidade exposta através da biblioteca do cliente lida com muitas tarefas, incluindo: gestão de ligações, gestão de fios, manuseamento de falhas e processamento de resultados intermédios. MSQ pode consultar até centenas de fragmentos. Para mais detalhes, consulte [a consulta multi-fragmentos.](elastic-scale-multishard-querying.md)

Em geral, os clientes que usam ferramentas Elastic Database podem esperar obter a funcionalidade T-SQL completa ao submeter operações shard-local em oposição a operações de cross-shard que têm a sua própria semântica.



## <a name="next-steps"></a>Passos seguintes

- Biblioteca de clientes Elastic Database [(Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - para **descarregar** a biblioteca.

- [Começa com ferramentas elásticas de base de dados](elastic-scale-get-started.md) - para experimentar a app de **amostras** que demonstra as funções do cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - para fazer contribuições para o código.
- [Visão geral da consulta elástica Azure SQL Database](elastic-query-overview.md) - para utilizar consultas elásticas.

- [Dados móveis entre bases de dados de nuvem escalonadas](elastic-scale-overview-split-and-merge.md) - para instruções sobre a utilização da **ferramenta de fusão dividida**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

