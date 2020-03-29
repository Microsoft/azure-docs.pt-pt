---
title: Recursos de Escala
description: Este artigo explica como escalar a sua base de dados adicionando ou removendo os recursos atribuídos.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835917"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Recursos de base de dados de escala dinâmica com tempo mínimo de inatividade

A Base de Dados Azure SQL permite-lhe adicionar dinamicamente mais recursos à sua base de dados com o mínimo de tempo de [inatividade;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) no entanto, há uma mudança ao longo do período em que a conectividade é perdida para a base de dados por um curto período de tempo, que pode ser atenuada usando a lógica de retry.

## <a name="overview"></a>Descrição geral

Quando a procura pela sua aplicação passa de um punhado de dispositivos e clientes para milhões, a Azure SQL Database escala no voo com o mínimo de tempo de inatividade. A escalabilidade é uma das características mais importantes do PaaS que lhe permite adicionar dinamicamente mais recursos ao seu serviço quando necessário. A Base de Dados Azure SQL permite-lhe alterar facilmente os recursos (energia CPU, memória, entrada iO e armazenamento) atribuídos às suas bases de dados.

Pode atenuar problemas de desempenho devido ao aumento do uso da sua aplicação que não pode ser corrigido utilizando métodos de indexação ou reescrita de consulta. Adicionar mais recursos permite-lhe reagir rapidamente quando a sua base de dados atinge os limites de recursos atuais e precisa de mais energia para lidar com a carga de trabalho que está a chegar. A Base de Dados Azure SQL também permite reduzir os recursos quando não são necessários para reduzir o custo.

Não precisas de te preocupar em comprar hardware e mudar as infraestruturas subjacentes. A base de dados de escalação pode ser facilmente feita através do portal Azure utilizando um slider.

![Desempenho da base de dados de escala](media/sql-database-scalability/scale-performance.svg)

A Azure SQL Database oferece o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore.](sql-database-service-tiers-vcore.md)

- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de recursos computacionais, de memória e IO em três níveis de serviço para suportar cargas de trabalho leves a pesados de base de dados: Básico, Standard e Premium. Os níveis de desempenho em cada camada fornecem uma mistura diferentes destes recursos, à qual pode adicionar recursos de armazenamento adicionais.
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite-lhe escolher o número de vCores, a quantidade ou memória, e a quantidade e velocidade de armazenamento. Este modelo de compra oferece três níveis de serviço: General Purpose, Business Critical e Hyperscale.

Pode construir a sua primeira aplicação numa pequena base de dados individual a um custo baixo por mês no nível de serviço Basic, Standard ou General Purpose e, em seguida, alterar o seu nível de serviço manual ou programática a qualquer momento para o nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender ao nível de serviço Premium ou Business Critical para atender o nível de serviço Premium ou Business Critical para atender o nível de serviço Premium ou Business Critical para atender o nível de serviço Premium ou Business Critical para atender o nível de serviço Premium ou necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. A escala automática é quando um serviço escala automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite escalar manual com um tempo mínimo de inatividade.

Uma Base de Dados SQL do Azure única suporta a escalabilidade dinâmica manual, mas não o dimensionamento automático. Para uma experiência mais *automática*, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados.
No entanto, existem scripts que podem ajudar a automatizar a escalabilidade para uma única Base de Dados Azure SQL. Por exemplo, veja [Utilizar o PowerShell para monitorizar e dimensionar uma Base de Dados SQL única](scripts/sql-database-monitor-and-scale-database-powershell.md).

Pode alterar os [escalões de serviço DTU](sql-database-service-tiers-dtu.md) ou as [caraterísticas vCore](sql-database-vcore-resource-limits-single-databases.md) a qualquer altura, com um período mínimo de indisponibilidade para a aplicação (em média, menos de quatro segundos). Para muitas empresas e aplicações, ser capaz de criar bases de dados e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Para este cenário, você usa um pool elástico com um certo número de EDTUs que são partilhados entre várias bases de dados na piscina.

![Introdução à Base de Dados SQL: DTUs de base de dados individual por camada e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Todos os três sabores da Base de Dados Azure SQL oferecem alguma capacidade de escalar dinamicamente as suas bases de dados:

- Com uma única base de [dados,](sql-database-single-database-scale.md)pode utilizar modelos [DTU](sql-database-dtu-resource-limits-single-databases.md) ou [vCore](sql-database-vcore-resource-limits-single-databases.md) para definir a quantidade máxima de recursos que serão atribuídos a cada base de dados.
- A [Managed Instance](sql-database-managed-instance.md) utiliza o modo [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) e permite-lhe definir os núcleos máximos de CPU e o máximo de armazenamento atribuído à sua instância. Todas as bases de dados da instância partilharão os recursos atribuídos à instância.
- [Piscinas elásticas](sql-database-elastic-pool-scale.md) permitem definir o limite máximo de recursos por grupo de bases de dados na piscina.

O início da escala para cima ou a redução da ação em qualquer um dos sabores reiniciaria o processo do motor da base de dados e movê-lo-ia para uma máquina virtual diferente, se necessário. Mover o processo do motor de base de dados para uma nova máquina virtual é **um processo online** onde pode continuar a utilizar o seu serviço de base de dados Azure SQL existente enquanto o processo está em curso. Uma vez que o motor de base de dados alvo esteja totalmente inicializado e pronto para processar as consultas, as ligações serão [mudadas de origem para motor](sql-database-single-database-scale.md#impact)de base de dados alvo . 


> [!NOTE]
> Pode esperar uma breve rutura de ligação quando o processo de escala para cima/escala para baixo estiver terminado. Se implementou a [lógica de Retry para erros transitórios padrão,](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)não vai notar a falha.

## <a name="alternative-scale-methods"></a>Métodos de escala alternativos

A escala de recursos é a forma mais fácil e eficaz de melhorar o desempenho da sua base de dados sem alterar a base de dados ou o código de aplicação. Em alguns casos, mesmo os mais altos níveis de serviço, tamanhos de computação e otimizações de desempenho podem não lidar com a sua carga de trabalho de forma bem sucedida e rentável. Nesse caso, tem estas opções adicionais para escalar a sua base de dados:

- [Ler scale-out](sql-database-read-scale-out.md) é uma funcionalidade disponível onde você está recebendo uma réplica apenas de leitura dos seus dados onde você pode executar consultas exigentes apenas de leitura, como relatórios. A réplica apenas para leitura irá lidar com a sua carga de trabalho apenas para leitura sem afetar o uso de recursos na sua base de dados primária.
- [A base](sql-database-elastic-scale-introduction.md) de dados é um conjunto de técnicas que lhe permite dividir os seus dados em várias bases de dados e escaloná-los de forma independente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a melhoria do desempenho da base de dados alterando o código de base de dados, consulte [Localizar e aplicar recomendações](sql-database-advisor-portal.md)de desempenho .
- Para obter informações sobre o que permite que a inteligência da base de dados incorporada otimize a sua base de dados, consulte [a afinação automática](sql-database-automatic-tuning.md).
- Para obter informações sobre a Read Scale-out no serviço de base de dados Azure SQL, consulte como [utilizar réplicas apenas](sql-database-read-scale-out.md)de leitura para carregar cargas de trabalho de consulta de leitura de equilíbrio .
- Para obter informações sobre uma base de dados, consulte [Scaling com base de dados Azure SQL](sql-database-elastic-scale-introduction.md).
