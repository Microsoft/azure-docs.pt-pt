---
title: O que é uma base de dados SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre a base de dados na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ad164e150b22af7d5a0c91fa0a8a80156952cfd0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431120"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é uma base de dados na base de dados do Azure SQL

A opção de implementação de base de dados cria uma base de dados na base de dados do Azure SQL com o seu próprio conjunto de recursos e é gerenciada através de um servidor de base de dados SQL. Com um único banco de dados, bases de dados estão isoladas umas das outras e portáteis, tendo cada um com sua própria camada de serviços dentro do [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e um garantida tamanho de computação.

> [!IMPORTANT]
> Base de dados é uma das três opções de implementação para a base de dados do Azure SQL. As outras duas estão [conjuntos elásticos](sql-database-elastic-pool.md) e [instância gerida](sql-database-managed-instance.md).
> [!NOTE]
> Para obter um glossário dos termos na base de dados do Azure SQL, consulte [Glossário de termos de base de dados SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Escalabilidade dinâmica

Pode criar sua primeira aplicação numa base de dados pequena e individual a um custo reduzido por mês no escalão de serviço de preço/desempenho fraco e, em seguida [alterar o escalão de serviço](sql-database-single-database-scale.md) manual ou programaticamente em qualquer altura para o serviço de preço/desempenho thehigher escalão para satisfazer as necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

## <a name="single-databases-and-elastic-pools"></a>Conjuntos elásticos e bases de dados individuais

Uma base de dados pode ser movido para dentro ou fora de um [conjunto elástico](sql-database-elastic-pool.md) para partilha de recursos. Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Conjuntos elásticos foram concebidos para resolver este problema. O conceito é simples. Alocar recursos de desempenho para um conjunto em vez de uma base de dados individual e pagar para os recursos de desempenho coletivo do conjunto em vez de desempenho de base de dados.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Utilizar o incorporado [monitorização de desempenho](sql-database-performance.md) e [ferramentas de alerta](sql-database-insights-alerts-portal.md), combinada com as classificações de desempenho. Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil.

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Bases de dados individuais, conjuntos elásticos e instâncias geridas fornecem muitas características de disponibilidade. Para obter informações, consulte [características de disponibilidade](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

A maioria das funcionalidades de Transact-SQL que utilizam aplicações são totalmente suportadas no Microsoft SQL Server e base de dados do Azure SQL. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, cadeia de caracteres, aritmética, lógicas e as funções de cursor, funcionam do mesmo modo no SQL Server e base de dados SQL. Existem, no entanto, algumas diferenças do T-SQL no DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que são suportadas apenas parcialmente (o que discutimos neste artigo).
Além disso, existem algumas funcionalidades e sintaxe que não é suportada em todos os porque a base de dados SQL do Azure foi concebida para isolar as funcionalidades de dependências na base de dados mestra e o sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a base de dados SQL. Instruções T-SQL e as opções não estão disponíveis caso configure opções ao nível do servidor, componentes do sistema operacional, ou especificar a configuração do sistema de ficheiros. Quando esses recursos são necessários, uma alternativa apropriada, muitas vezes, está disponível de outro modo da base de dados SQL ou do Azure outra funcionalidade ou serviço.

Para obter mais informações, consulte [diferenças de resolução de Transact-SQL durante a migração para a base de dados SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Segurança

Base de dados SQL oferece uma gama de [segurança incorporadas e compatibilidade](sql-database-security-overview.md) recursos para ajudar a sua aplicação a cumprir diversos requisitos de segurança e conformidade.

## <a name="next-steps"></a>Passos Seguintes

- Para começar rapidamente com uma base de dados, comece com o [único guide.md de início rápido de base de dados](sql-database-single-database-quickstart-guide.md).
- Para saber mais sobre como migrar uma base de dados do SQL Server para o Azure, veja [migre para o Azure SQL Database](sql-database-single-database-migrate.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).
