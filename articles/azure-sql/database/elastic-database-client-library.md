---
title: Criar bases de dados de nuvem escaláveis
description: Construa aplicações de base de dados escaláveis .NET com a biblioteca de clientes da Base de Dados Elástica.
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
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050227"
---
# <a name="building-scalable-cloud-databases"></a>Criar bases de dados de nuvem escaláveis
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A escala de bases de dados pode ser facilmente realizada utilizando ferramentas e funcionalidades escaláveis para a Base de Dados Azure SQL. Em particular, pode utilizar a biblioteca de clientes Da Base de **Dados Elásticas** para criar e gerir bases de dados dimensionadas. Esta funcionalidade permite desenvolver facilmente aplicações esfumaçadas utilizando centenas ou mesmo milhares de bases de dados na Base de Dados Azure SQL.

Para descarregar:

* A versão Java da biblioteca, ver [Maven Central Repositório](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão .NET da biblioteca, ver [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentação

1. [Introdução às ferramentas de Base de Dados Elástica](elastic-scale-get-started.md)
2. [Características da Base de Dados Elástica](elastic-scale-introduction.md)
3. [Gestão de mapas de partições horizontais](elastic-scale-shard-map-management.md)
4. [Migrar bases de dados existentes para escalar](elastic-convert-to-use-elastic-tools.md)
5. [Encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md)
6. [Consultas de vários fragmentos](elastic-scale-multishard-querying.md)
7. [Adicionar um fragmento usando ferramentas de base de dados elásticas](elastic-scale-add-a-shard.md)
8. [Aplicações multi-inquilinos com ferramentas de base de dados elásticas e segurança ao nível da linha](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicativos da biblioteca de clientes](elastic-scale-upgrade-client-library.md) 
10. [Visão geral de consultas elásticas](elastic-query-overview.md)
11. [Glossário de ferramentas de base de dados elástica](elastic-scale-glossary.md)
12. [Biblioteca de clientes de base de dados elástica com quadro de entidade](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de clientes de base de dados elástica com Dapper](elastic-scale-working-with-dapper.md)
14. [Ferramenta de fusão dividida](elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gestor de mapas de partições horizontais](elastic-database-client-library.md) 
16. [FAQ para ferramentas de base de dados elásticas](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Capacidades do cliente

A redução das aplicações utilizando *sharding* apresenta desafios tanto para o desenvolvedor como para o administrador. A biblioteca de clientes simplifica as tarefas de gestão fornecendo ferramentas que permitem que tanto os desenvolvedores como os administradores gerem bases de dados dimensionadas. Num exemplo típico, existem muitas bases de dados, conhecidas como "fragmentos", para gerir. Os clientes são co-localizados na mesma base de dados, e há uma base de dados por cliente (um regime de inquilino único). A biblioteca do cliente inclui estas funcionalidades:

- **Gestão de mapas de fragmentos**: É criada uma base de dados especial chamada "gestor de mapas de fragmentos". A gestão de mapas de fragmentos é a capacidade de uma aplicação gerir metadados sobre os seus fragmentos. Os desenvolvedores podem usar esta funcionalidade para registar bases de dados como fragmentos, descrever mapeamentos de chaves de sharding individuais ou intervalos de chaves para essas bases de dados, e manter estes metadados à medida que o número e composição das bases de dados evolui para refletir as mudanças de capacidade. Sem a biblioteca de clientes da Base de Dados Elástica, teria de passar muito tempo a escrever o código de gestão na implementação do sharding. Para mais detalhes, consulte a gestão do [mapa de Shard.](elastic-scale-shard-map-management.md)

- **Encaminhamento dependente**de dados : Imagine um pedido que entra na aplicação. Com base no valor-chave do pedido, a aplicação precisa de determinar a base de dados correta com base no valor-chave. Em seguida, abre uma ligação à base de dados para processar o pedido. O encaminhamento dependente de dados fornece a capacidade de abrir ligações com uma única chamada fácil para o mapa de fragmentos da aplicação. O encaminhamento dependente de dados foi outra área do código de infraestrutura que está agora coberta pela funcionalidade na biblioteca de clientes da Base de Dados Elástica. Para mais detalhes, consulte o [encaminhamento dependente de Dados](elastic-scale-data-dependent-routing.md).
- **Consultas multi-fragmentos (MSQ)**: A consulta multi-fragmento funciona quando um pedido envolve vários (ou todos) fragmentos. Uma consulta multi-fragmento executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados dos fragmentos participantes são fundidos num conjunto de resultados globais utilizando a semântica UNION ALL. A funcionalidade exposta através da biblioteca do cliente lida com muitas tarefas, incluindo: gestão de ligação, gestão de fios, manuseamento de falhas e processamento de resultados intermédios. O MSQ pode consultar centenas de fragmentos. Para mais detalhes, consulte a [consulta multi-fragmento](elastic-scale-multishard-querying.md).

Em geral, os clientes que usam ferramentas de Base de Dados Elásticas podem esperar obter a funcionalidade T-SQL completa ao submeter operações locais de caco em oposição a operações cruzadas que têm a sua própria semântica.



## <a name="next-steps"></a>Próximos passos

- Biblioteca de clientes da Base de Dados Elástica[(Java,](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22) [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - para **descarregar** a biblioteca.

- [Inicie-se com ferramentas de Base](elastic-scale-get-started.md) de Dados Elásticas - para experimentar a **aplicação de amostras** que demonstra as funções do cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - para fazer contribuições para o código.
- [Visão geral de consulta elástica Azure SQL Base](elastic-query-overview.md) de Dados - para usar consultas elásticas.

- Mover dados entre bases de dados em [nuvem escaldadas](elastic-scale-overview-split-and-merge.md) - para instruções sobre a utilização da **ferramenta de fusão split -.**



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

