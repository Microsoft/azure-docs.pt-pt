---
title: O que é uma única base de dados?
description: Conheça o tipo de recurso único da base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: f6b705da74fc939cab5f72e6335278207b98dc1a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041765"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é uma única base de dados na Base de Dados Azure SQL?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O tipo único de recurso de base de dados cria uma base de dados na Base de Dados Azure SQL com o seu próprio conjunto de recursos e é gerido através de um [servidor](logical-servers.md). Com uma única base de dados, cada base de dados é isolada uma da outra e portátil, cada uma com o seu próprio nível de serviço dentro do modelo de [compra baseado em DTU](service-tiers-dtu.md) ou [modelo de compra baseado em vCore](service-tiers-vcore.md) e um tamanho de cálculo garantido.

> [!IMPORTANT]
> A base de dados única é um tipo de recurso para a Base de Dados Azure SQL. O outro são [piscinas elásticas.](elastic-pool-overview.md)

## <a name="dynamic-scalability"></a>Escalabilidade dinâmica

Pode construir a sua primeira aplicação numa pequena base de dados única a baixo custo no nível de computação sem servidores ou num pequeno tamanho de computação no nível de cálculo provisionado. Altera o nível de [computação ou de serviço](single-database-scale.md) manualmente ou programáticamente a qualquer momento para satisfazer as necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

## <a name="single-databases-and-elastic-pools"></a>Bases de dados individuais e conjuntos elásticos

Uma única base de dados pode ser movida para dentro ou para fora de um [pool elástico](elastic-pool-overview.md) para partilha de recursos. Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os conjuntos elásticos foram concebidos para resolver este problema. O conceito é simples. Você aloca recursos de desempenho a uma piscina em vez de uma base de dados individual e paga pelos recursos de desempenho coletivos da piscina em vez de um desempenho único na base de dados.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Utiliza as ferramentas de [monitorização](performance-guidance.md) e alerta de desempenho [incorporadas,](alerts-insights-configure-portal.md)combinadas com as classificações de desempenho. Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Além disso, a Base de Dados SQL pode [emitir métricas e registos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) de recursos para uma monitorização mais fácil.

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Bases de dados únicas e piscinas elásticas proporcionam muitas características de disponibilidade. Para obter informações, consulte [as características de disponibilidade.](sql-database-paas-overview.md#availability-capabilities)

## <a name="transact-sql-differences"></a>Diferenças Transact-SQL

A maioria das funcionalidades Transact-SQL que as aplicações utilizam são totalmente suportadas tanto no Microsoft SQL Server como na Base de Dados Azure SQL. Por exemplo, os componentes SQL fundamentais, tais como tipos de dados, operadores, funções de cadeia, aritmética, lógica e cursor, funcionam de forma idêntica no Servidor SQL e na Base de Dados SQL. Existem, no entanto, algumas diferenças T-SQL nos elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados) que resultam em declarações e consultas T-SQL que só são parcialmente suportadas (que discutimos mais tarde neste artigo).

Além disso, existem algumas funcionalidades e sintaxe que não são suportadas porque a Base de Dados Azure SQL foi projetada para isolar funcionalidades das dependências da base de dados principal e do sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a Base de Dados SQL. As declarações e opções T-SQL não estão disponíveis se configurarem opções de nível de servidor, componentes do sistema operativo ou especificarem a configuração do sistema de ficheiros. Quando tais capacidades são necessárias, uma alternativa adequada está muitas vezes disponível de outra forma a partir da Base de Dados SQL ou de outra funcionalidade ou serviço Azure.

Para obter mais informações, consulte a Resolução das [diferenças Transact-SQL durante](transact-sql-tsql-differences-sql-server.md)a migração para a Base de Dados SQL .

## <a name="security"></a>Segurança

A Base de Dados SQL fornece uma gama de funcionalidades de [segurança e conformidade incorporadas](security-overview.md) para ajudar a sua aplicação a satisfazer vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Base de Dados Azure SQL foi certificada contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

## <a name="next-steps"></a>Próximos passos

- Para começar rapidamente com uma única base de dados, comece com o [guia de arranque rápido da base de dados Single](quickstart-content-reference-guide.md).
- Para saber sobre a migração de uma base de dados do SQL Server para o Azure, consulte [migrate para azure SQL Database](migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](features-comparison.md).
