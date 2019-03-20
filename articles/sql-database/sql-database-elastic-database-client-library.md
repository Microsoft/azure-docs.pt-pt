---
title: Criar bases de dados dimensionáveis da cloud | Documentos da Microsoft
description: Criar aplicações escaláveis de base de dados .NET com a biblioteca de cliente da base de dados elástica
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: c0d50f3a66d940618f2bc421537b113120a2eaca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006295"
---
# <a name="building-scalable-cloud-databases"></a>Criar bases de dados de nuvem escaláveis

Aumentar horizontalmente de bases de dados pode ser facilmente feito usando ferramentas dimensionáveis e funcionalidades para a base de dados do Azure SQL. Em particular, pode utilizar o **biblioteca de clientes de bases de dados elásticas** para criar e gerir bases de dados de escalamento horizontal. Esta funcionalidade permite-lhe desenvolver facilmente aplicações em partição horizontal, com centenas – ou até mesmo milhares – de bases de dados SQL do Azure. [Tarefas elásticas](sql-database-elastic-jobs-powershell.md) , em seguida, pode ser utilizado para o ajudar a facilidade de gestão destas bases de dados.

Para transferir:

* A versão de Java da biblioteca, consulte [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentação

1. [Introdução às ferramentas de Base de Dados Elástica](sql-database-elastic-scale-get-started.md)
2. [Recursos de banco de dados elásticos](sql-database-elastic-scale-introduction.md)
3. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
4. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
6. [Consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md)
7. [Adicionar uma partição horizontal com as ferramentas de bases de dados elásticas](sql-database-elastic-scale-add-a-shard.md)
8. [Aplicações multi-inquilino com ferramentas de base de dados elástica e segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicações da biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Descrição geral de consultas elásticas](sql-database-elastic-query-overview.md)
11. [Glossário das ferramentas das bases de dados elásticas](sql-database-elastic-scale-glossary.md)
12. [Biblioteca de clientes de base de dados elástica com Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de clientes de bases de dados elásticas com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Ferramenta de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-client-library.md) 
16. [FAQ sobre ferramentas de bases de dados elásticas](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Funcionalidades de cliente

Ampliar aplicativos usando *fragmentação* apresenta desafios para tanto o desenvolvedor, bem como o administrador. A biblioteca de cliente simplifica as tarefas de gestão, fornecendo ferramentas que permitem que desenvolvedores e administradores gerem bases de dados de escalamento horizontal. Um exemplo típico, existem muitas bases de dados, conhecidos como "partições horizontais," para gerir. Os clientes são colocalizados na mesma base de dados, e há um banco de dados por cliente (um esquema de inquilino único). A biblioteca de clientes inclui estas funcionalidades:

- **Gestão de mapas de partições horizontais**: É criada uma base de dados especial chamado o "Gestor de mapas de partições horizontais". Gestão de mapas de partições horizontais é a capacidade para uma aplicação Gerir metadados sobre suas partições horizontais. Os programadores podem utilizar esta funcionalidade para registrar as bases de dados como as partições horizontais, descrevem os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para essas bases de dados e manter esses metadados, como o número e composição de bases de dados se desenvolve para refletir as alterações de capacidade. Sem a biblioteca de cliente da base de dados elástica, terá de gastar muito tempo escrevendo o código de gestão quando a implementação da fragmentação. Para obter detalhes, consulte [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

- **Encaminhamento dependente de dados**: Imagine um pedido para o aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar a base de dados correta com base no valor da chave. Em seguida, abre-se uma ligação à base de dados para processar o pedido. Encaminhamento dependente de dados fornece a capacidade de abrir ligações com uma única chamada simples para o mapa de fragmentação da aplicação. Encaminhamento dependente de dados era a outra área de código de infraestrutura que agora é abrangido por funcionalidade na biblioteca de cliente da base de dados elásticas. Para obter detalhes, consulte [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).
- **Consultas de vários fragmentos (MSQ)**: Consultas de vários fragmentos funcionam quando uma solicitação envolve várias (ou todos os) as partições horizontais. Uma consulta de vários fragmentos executa o mesmo código de T-SQL em todas as partições horizontais ou um conjunto de partições horizontais. Os resultados de partições horizontais participantes são mesclados num resultado geral do conjunto com a semântica UNION ALL. A funcionalidade conforme exposto por meio da biblioteca de cliente lida com várias tarefas, incluindo: gerenciamento de conexões, gerenciamento de threads, processamento de falhas e os resultados intermediários de processamento. MSQ pode consultar até centenas de partições horizontais. Para obter detalhes, consulte [consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).

Em geral, os clientes que utilizam as ferramentas de bases de dados elásticas podem esperar obter todas as funcionalidades T-SQL ao submeter as operações de partição horizontal-local em vez de operações de entre partições horizontais que têm suas próprias semânticas.



## <a name="next-steps"></a>Passos Seguintes

- Biblioteca de clientes de base de dados elástica ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – para **transferir** a biblioteca.

- [Introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md) - para experimentar o **aplicação de exemplo** que demonstra as funções do cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – para realizar contribuições para o código.
- [Descrição geral de consulta elástica de base de dados SQL do Azure](sql-database-elastic-query-overview.md) - para utilizar consultas elásticas.

- [Mover dados entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md) – para obter instruções sobre como utilizar o **ferramenta de dividir / unir**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

