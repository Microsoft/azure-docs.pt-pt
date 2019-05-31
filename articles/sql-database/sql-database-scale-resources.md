---
title: Recursos de dimensionamento de banco de dados SQL do Azure | Documentos da Microsoft
description: Este artigo explica como dimensionar a sua base de dados ao adicionar ou remover recursos alocados.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e476f27e2a1945135bd90435078d5bcd47c4b3de
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073141"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dimensionar dinamicamente recursos de banco de dados com o tempo de inatividade mínimo

Base de dados SQL do Azure permite-lhe adicionar dinamicamente mais recursos à base de dados com o período de indisponibilidade mínimo.

## <a name="overview"></a>Descrição geral

Quando a pedido para a sua aplicação cresce de meia dúzia de dispositivos e clientes para milhões, a base de dados do Azure SQL dimensiona rapidamente sem período de indisponibilidade mínimo. A escalabilidade é uma das características mais importantes de PaaS que permite-lhe adicionar dinamicamente mais recursos ao seu serviço quando necessário. Base de dados SQL do Azure permite-lhe alterar facilmente a recursos (potência da CPU, memória, débito de e/s e armazenamento) alocados para seus bancos de dados.

Pode mitigar problemas de desempenho devido à maior utilização da sua aplicação que não é possível corrigir com indexação ou métodos de reescrita de consulta. Adição de mais recursos permite-lhe reagir rapidamente quando a base de dados atinge os limites de recursos atual e necessita de mais poder para processar a carga de trabalho de entrada. Base de dados SQL do Azure também permite-lhe reduzir verticalmente os recursos que não são necessários para reduzir o custo.

Não precisa se preocupar sobre a compra de hardware e alterar a infraestrutura subjacente. Dimensionamento da base de dados pode ser facilmente feito através do portal do Azure com um controlo de deslize.

![Aumente o desempenho da base de dados](media/sql-database-scalability/scale-performance.svg)

Base de dados SQL do Azure oferece a [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece um blend de computação, memória e recursos de e/s em três escalões de serviço para suportar cargas de trabalho de base de dados de pedidos leves: Básico, Standard e Premium. Os níveis de desempenho em cada camada fornecem uma mistura diferentes destes recursos, à qual pode adicionar recursos de armazenamento adicionais.
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite-lhe escolher o número de vCores a quantidade ou memória e a quantidade e velocidade de armazenamento. Este modelo de compra oferece três escalões de serviço: Fins gerais, a empresa crítica e a Hiperescala.

Pode criar a primeira aplicação numa base de dados pequena e individual a um custo reduzido por mês no escalão de serviço básico, Standard ou fins gerais e, em seguida, alterar o escalão de serviço manual ou programaticamente em qualquer altura para o escalão de serviço Premium ou críticas para a empresa para satisfazer o ne eds da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual sem períodos de indisponibilidade.

Uma Base de Dados SQL do Azure única suporta a escalabilidade dinâmica manual, mas não o dimensionamento automático. Para uma experiência mais *automática*, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados.
No entanto, existem scripts que podem ajudar a automatizar a escalabilidade para uma única base de dados do Azure SQL. Por exemplo, veja [Utilizar o PowerShell para monitorizar e dimensionar uma Base de Dados SQL única](scripts/sql-database-monitor-and-scale-database-powershell.md).

Pode alterar [escalões de serviço DTU](sql-database-service-tiers-dtu.md) ou [características de vCore](sql-database-vcore-resource-limits-single-databases.md) em qualquer altura com o período de indisponibilidade mínimo para a sua aplicação (em média, menos de quatro segundos). Para muitas empresas e aplicações, ser capaz de criar bases de dados e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Para este cenário, utilize um conjunto elástico com um determinado número de eDTUs que são partilhados entre várias bases de dados no conjunto.

![Introdução à base de dados SQL: Base de dados DTUs por camada e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Todos os três tipos de base de dados do Azure SQL oferecem alguma possibilidade de dimensionar dinamicamente o seus bancos de dados:

- Com um [base de dados individual](sql-database-single-database-scale.md), pode utilizar tanto [DTU](sql-database-dtu-resource-limits-single-databases.md) ou [vCore](sql-database-vcore-resource-limits-single-databases.md) modelos para definir a quantidade máxima de recursos que serão atribuídos a cada base de dados.
- R [instância gerida](sql-database-managed-instance.md) utiliza [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) modo e permite-lhe definir o máximo de núcleos de CPU e máximo de armazenamento atribuído à sua instância. Todas as bases de dados dentro da instância irão partilhar os recursos alocados à instância.
- [Conjuntos elásticos](sql-database-elastic-pool-scale.md) permitem-lhe definir o limite máximo de recursos por grupo de bases de dados no conjunto.

## <a name="alternative-scale-methods"></a>Métodos de dimensionamento alternativo

Dimensionar os recursos é o mais fácil e a forma mais eficiente para melhorar o desempenho da base de dados sem alterar o código de base de dados ou aplicação. Em alguns casos, até mesmo os escalões de serviço mais elevados, tamanhos de computação e otimizações de desempenho não podem processar a carga de trabalho na forma com êxito e econômica. Em que casos tem estas opções adicionais para dimensionar a sua base de dados:

- [Escalamento leitura](sql-database-read-scale-out.md) é uma funcionalidade disponível em onde está a obter uma réplica só de leitura dos seus dados onde pode executar a exigência de consultas só de leitura, como relatórios. Réplica só de leitura irá processar a carga de trabalho só de leitura sem afetar a utilização de recursos na sua base de dados primária.
- [Fragmentação de base de dados](sql-database-elastic-scale-introduction.md) é um conjunto de técnicas que lhe permite dividir os dados em várias bases de dados e dimensionar de forma independente.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como melhorar o desempenho de base de dados ao alterar o código de base de dados, consulte [localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para obter informações sobre o que lhe permite otimizar a sua base de dados de inteligência de base de dados incorporada, consulte [otimização automática](sql-database-automatic-tuning.md).
- Para obter informações sobre o Escalamento de leitura no serviço de base de dados do Azure SQL, consulte como [utilizar réplicas só de leitura para cargas de trabalho de consulta só de leitura de balanceamento de carga](sql-database-read-scale-out.md).
- Para obter informações sobre uma fragmentação de base de dados, consulte [aumentar horizontalmente com a base de dados do Azure SQL](sql-database-elastic-scale-introduction.md).
