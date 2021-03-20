---
title: O que é uma única base de dados?
description: Saiba mais sobre o tipo único de recursos de base de dados na Base de Dados Azure SQL.
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
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343324"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é uma única base de dados na Base de Dados Azure SQL?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O tipo único de recursos de base de dados cria uma base de dados na Base de Dados Azure SQL com o seu próprio conjunto de recursos e é gerido através de um [servidor](logical-servers.md). Com uma única base de dados, cada base de dados é isolada e portátil. Cada um tem o seu próprio nível de serviço dentro do [modelo de compra baseado em DTU](service-tiers-dtu.md) ou modelo de compra baseado em [vCore](service-tiers-vcore.md) e um tamanho de computação garantido.

> [!IMPORTANT]
> A base de dados única é um tipo de recurso para a Base de Dados Azure SQL. A outra são [piscinas elásticas.](elastic-pool-overview.md)

## <a name="dynamic-scalability"></a>Escalabilidade dinâmica

Pode construir a sua primeira aplicação numa base de dados pequena e única a baixo custo no nível de computação sem servidor ou num pequeno tamanho de computação no nível de computação provisionado. Altere o [nível de cálculo ou serviço](single-database-scale.md) manualmente ou programático a qualquer momento para satisfazer as necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

## <a name="single-databases-and-elastic-pools"></a>Bases de dados individuais e conjuntos elásticos

Uma única base de dados pode ser movida para dentro ou para fora de uma [piscina elástica](elastic-pool-overview.md) para a partilha de recursos. Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os conjuntos elásticos foram concebidos para resolver este problema. O conceito é simples. Aloque recursos de desempenho a um pool em vez de uma base de dados individual e pague os recursos de desempenho coletivo do pool em vez do desempenho de uma única base de dados.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Utiliza as ferramentas de [monitorização](performance-guidance.md) e alerta de desempenho [incorporadas,](alerts-insights-configure-portal.md)combinadas com as classificações de desempenho. Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Além disso, a BASE de Dados SQL pode [emitir métricas e registos de recursos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para uma monitorização mais fácil.

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Bases de dados únicas e piscinas elásticas proporcionam muitas características de disponibilidade. Para obter informações, consulte [as características de Disponibilidade.](sql-database-paas-overview.md#availability-capabilities)

## <a name="transact-sql-differences"></a>Diferenças transact-SQL

A maioria das funcionalidades Transact-SQL que as aplicações utilizam são totalmente suportadas tanto no Microsoft SQL Server como no Azure SQL Database. Por exemplo, os componentes SQL de núcleo, tais como tipos de dados, operadores, funções de cadeia, aritmética, lógica e cursor, funcionam de forma idêntica no SQL Server e na SQL Database. Existem, no entanto, algumas diferenças de T-SQL em elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados) que resultam em declarações e consultas T-SQL que só são parcialmente suportadas (que discutimos mais tarde neste artigo).

Além disso, existem algumas funcionalidades e sintaxe que não são suportadas porque a Base de Dados Azure SQL foi concebida para isolar características das dependências da base de dados principal e do sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a Base de Dados SQL. As declarações e opções T-SQL não estão disponíveis se configurarem opções de nível do servidor, configurarem componentes do sistema operativo ou especificarem a configuração do sistema de ficheiros. Quando tais capacidades são necessárias, uma alternativa apropriada está frequentemente disponível de outra forma a partir da Base de Dados SQL ou de outra funcionalidade ou serviço Azure.

Para obter mais informações, consulte [a Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL.](transact-sql-tsql-differences-sql-server.md)

## <a name="security"></a>Segurança

A SQL Database fornece uma gama de funcionalidades de [segurança e conformidade incorporadas](security-overview.md) para ajudar a sua aplicação a cumprir vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> A Azure SQL Database foi certificada contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)onde pode encontrar a lista mais atual de certificações de conformidade da Base de Dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para começar rapidamente com uma única base de dados, comece com o [guia quickstart da base de dados single](quickstart-content-reference-guide.md).
- Para saber sobre a migração de uma base de dados do SQL Server para Azure, consulte [a Migração para a Base de Dados SQL Azure](migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](features-comparison.md).
