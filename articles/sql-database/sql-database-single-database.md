---
title: O que é um banco de dados individual do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre o banco de dados individual no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: c5de4277915e71763f034596d372554e4f88e204
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316667"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é um banco de dados individual no banco de dados SQL do Azure

A opção de implantação de banco de dados individual cria um banco de dados no banco de dados SQL do Azure com seu próprio conjunto de recursos e é gerenciado por meio de um servidor de banco de dados SQL Com um único banco de dados, cada banco de dados é isolado entre si e portátil, cada um com sua própria camada de serviço dentro do [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e um tamanho de computação garantido.

> [!IMPORTANT]
> Banco de dados individual é uma das três opções de implantação para o banco de dados SQL do Azure. Os outros dois são [pools elásticos](sql-database-elastic-pool.md) e [instância gerenciada](sql-database-managed-instance.md).
> [!NOTE]
> Para obter um glossário de termos no banco de dados SQL do Azure, consulte [Glossário de termos do banco de dados SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Escalabilidade dinâmica

Você pode criar seu primeiro aplicativo em um banco de dados pequeno e único com baixo custo na camada de computação sem servidor (visualização) ou em um tamanho de computação pequeno na camada de computação provisionada. Você altera a [camada de computação ou de serviço](sql-database-single-database-scale.md) manualmente ou programaticamente a qualquer momento para atender às necessidades de sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

## <a name="single-databases-and-elastic-pools"></a>Bases de dados individuais e conjuntos elásticos

Um único banco de dados pode ser movido para dentro ou para fora de um [pool elástico](sql-database-elastic-pool.md) para compartilhamento de recursos. Para muitas empresas e aplicações, ser capaz de criar bases de dados individuais e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os pools elásticos são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho para um pool em vez de um banco de dados individual e paga pelos recursos de desempenho coletivo do pool em vez de ter um desempenho de banco de dados único.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Você usa as ferramentas internas de [monitoramento](sql-database-performance.md) e [alerta](sql-database-insights-alerts-portal.md)de desempenho, combinadas com as classificações de desempenho. Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Além disso, a Base de Dados SQL pode [emitir métricas e registos de diagnósticos](sql-database-metrics-diag-logging.md) para uma monitorização mais fácil.

## <a name="availability-capabilities"></a>Capacidades de disponibilidade

Os bancos de dados individuais, os pools elásticos e as instâncias gerenciadas oferecem muitas características de disponibilidade. Para obter informações, consulte [características de disponibilidade](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

A maioria dos recursos do Transact-SQL usados por aplicativos tem suporte total em Microsoft SQL Server e no banco de dados SQL do Azure. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, Cadeia de caracteres, aritmética, lógica e funções de cursor, funcionam de forma idêntica no SQL Server e no banco de dados SQL. No entanto, há algumas diferenças de T-SQL nos elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que só têm suporte parcial (o que discutiremos mais adiante neste artigo).
Além disso, há alguns recursos e sintaxe que não têm suporte porque o banco de dados SQL do Azure foi projetado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades de nível de servidor são inadequadas para o banco de dados SQL. As instruções e as opções do T-SQL não estarão disponíveis se configurarem opções no nível do servidor, componentes do sistema operacional ou especificarão a configuração do sistema de arquivos. Quando esses recursos são necessários, uma alternativa apropriada geralmente está disponível de alguma forma do banco de dados SQL ou de outro recurso ou serviço do Azure.

Para obter mais informações, consulte [resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Segurança

O banco de dados SQL fornece uma variedade de recursos [internos de segurança e conformidade](sql-database-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> O banco de dados SQL do Azure (todas as opções de implantação) foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para começar rapidamente com um único banco de dados, comece com o [Guia de início rápido do banco de dados individual](sql-database-single-database-quickstart-guide.md).
- Para saber mais sobre como migrar um banco de dados SQL Server para o Azure, consulte [migrar para o banco de dados SQL do Azure](sql-database-single-database-migrate.md)
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).
