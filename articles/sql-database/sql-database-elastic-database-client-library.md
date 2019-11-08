---
title: Criar bases de dados de nuvem escaláveis
description: Crie aplicativos de banco de dados .NET escalonáveis com a biblioteca de cliente do banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823913"
---
# <a name="building-scalable-cloud-databases"></a>Criar bases de dados de nuvem escaláveis

A expansão de bancos de dados pode ser facilmente realizada usando ferramentas e recursos escalonáveis para o banco de dados SQL do Azure. Em particular, você pode usar a **biblioteca de cliente do banco de dados elástico** para criar e gerenciar bancos de dados escalados horizontalmente. Esse recurso permite que você desenvolva facilmente aplicativos fragmentados usando centenas (ou até mesmo milhares) de bancos de dados SQL do Azure.

Para baixar:

* A versão do Java da biblioteca, consulte [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentação

1. [Introdução às ferramentas de Base de Dados Elástica](sql-database-elastic-scale-get-started.md)
2. [Recursos de banco de dados elástico](sql-database-elastic-scale-introduction.md)
3. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
4. [Migrar bancos de dados existentes para escalar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
6. [Consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md)
7. [Adicionando um fragmento usando ferramentas de banco de dados elástico](sql-database-elastic-scale-add-a-shard.md)
8. [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicativos de biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Visão geral das consultas elásticas](sql-database-elastic-query-overview.md)
11. [Glossário das ferramentas das bases de dados elásticas](sql-database-elastic-scale-glossary.md)
12. [Biblioteca de cliente do banco de dados elástico com Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de cliente do banco de dados elástico com Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Ferramenta de mesclagem/divisão](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-client-library.md) 
16. [Perguntas frequentes para ferramentas de banco de dados elástico](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Recursos do cliente

Expandir os aplicativos usando a *fragmentação* apresenta desafios para o desenvolvedor, bem como para o administrador. A biblioteca de cliente simplifica as tarefas de gerenciamento fornecendo ferramentas que permitem que os desenvolvedores e administradores gerenciem bancos de dados escalados horizontalmente. Em um exemplo típico, há muitos bancos de dados, conhecidos como "fragmentos", a serem gerenciados. Os clientes estão colocalizados no mesmo banco de dados, e há um banco de dados por cliente (um esquema de locatário único). A biblioteca de cliente inclui estes recursos:

- **Gerenciamento de mapa de fragmentos**: um banco de dados especial chamado "Gerenciador de mapa de fragmentos" é criado. O gerenciamento de mapa de fragmentos é a capacidade de um aplicativo gerenciar metadados sobre seus fragmentos. Os desenvolvedores podem usar essa funcionalidade para registrar bancos de dados como fragmentos, descrever mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para esses bancos de dados e manter esses metadados à medida que o número e a composição de bancos de dados evoluem para refletir as alterações de capacidade. Sem a biblioteca de cliente do banco de dados elástico, você precisaria gastar muito tempo escrevendo o código de gerenciamento ao implementar a fragmentação. Para obter detalhes, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).

- **Roteamento Dependente de dados**: Imagine uma solicitação que entra no aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar o banco de dados correto com base no valor da chave. Em seguida, ele abre uma conexão com o banco de dados para processar a solicitação. O roteamento dependente de dados fornece a capacidade de abrir conexões com uma única chamada fácil no mapa de fragmentos do aplicativo. O roteamento dependente de dados era outra área de código de infraestrutura que agora é abordada pela funcionalidade na biblioteca de cliente do banco de dados elástico. Para obter detalhes, consulte [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).
- **Consultas de vários fragmentos (MSQ)** : a consulta de vários fragmentos funciona quando uma solicitação envolve vários fragmentos (ou todos). Uma consulta de vários fragmentos executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados dos fragmentos participantes são mesclados em um conjunto de resultados geral usando a semântica UNION ALL. A funcionalidade exposta por meio da biblioteca de cliente lida com muitas tarefas, incluindo: gerenciamento de conexão, gerenciamento de threads, tratamento de falhas e processamento de resultados intermediários. O MSQ pode consultar até centenas de fragmentos. Para obter detalhes, consulte [consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).

Em geral, os clientes que usam as ferramentas de banco de dados elástico podem esperar obter a funcionalidade completa do T-SQL ao enviar operações de fragmento local em oposição a operações entre fragmentos que têm sua própria semântica.



## <a name="next-steps"></a>Passos seguintes

- Biblioteca de cliente do banco de dados elástico ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – para **baixar** a biblioteca.

- [Introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) – para experimentar o **aplicativo de exemplo** que demonstra as funções de cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.net](https://github.com/Azure/elastic-db-tools)) – para fazer contribuições para o código.
- [Visão geral da consulta elástica do banco de dados SQL do Azure](sql-database-elastic-query-overview.md) -para usar consultas elásticas.

- [Movendo dados entre bancos de dado de nuvem expandidos](sql-database-elastic-scale-overview-split-and-merge.md) -para obter instruções sobre como usar a **ferramenta de divisão/mesclagem**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

