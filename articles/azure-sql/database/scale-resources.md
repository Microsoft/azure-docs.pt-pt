---
title: Dimensionar recursos
description: Este artigo explica como escalar a sua base de dados na Base de Dados Azure SQL e na SQL Managed Instance adicionando ou removendo recursos atribuídos.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 453d7e118b946d60eb3d84c6a66abdbea7db2410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499225"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Recursos de base de dados de escala dinâmica com tempo mínimo de inatividade
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database e SQL Managed Instance permitem-lhe adicionar dinamicamente mais recursos à sua base de dados com tempo de [inatividade](https://azure.microsoft.com/support/legal/sla/sql-database)mínimo; no entanto, há um interruptor ao longo do período em que a conectividade é perdida para a base de dados por um curto período de tempo, que pode ser atenuada usando a lógica de retrip.

## <a name="overview"></a>Descrição geral

Quando a procura pela sua aplicação cresce de um punhado de dispositivos e clientes para milhões, a Base de Dados Azure SQL e a escala sql Managed Instance no voo com o mínimo de tempo de inatividade. A escalabilidade é uma das características mais importantes da plataforma como um serviço (PaaS) que lhe permite adicionar dinamicamente mais recursos ao seu serviço quando necessário. A Azure SQL Database permite-lhe alterar facilmente os recursos (energia CPU, memória, produção de IO e armazenamento) alocados às suas bases de dados.

Pode atenuar problemas de desempenho devido ao uso aumentado da sua aplicação que não pode ser corrigido usando métodos de indexação ou de reescrita de consultas. A adição de mais recursos permite-lhe reagir rapidamente quando a sua base de dados atinge os limites de recursos atuais e precisa de mais energia para lidar com a carga de trabalho que está a chegar. A Base de Dados Azure SQL também permite reduzir os recursos quando não são necessários para baixar o custo.

Não precisa de se preocupar em comprar hardware e mudar a infraestrutura subjacente. A escala de uma base de dados pode ser facilmente feita através do portal Azure utilizando um slider.

![Desempenho da base de dados de escala](./media/scale-resources/scale-performance.svg)

A Azure SQL Database oferece o [modelo de compra baseado em DTU](service-tiers-dtu.md) e o modelo de compra baseado em [vCore,](service-tiers-vcore.md)enquanto a Azure SQL Managed Instance oferece apenas o [modelo de compra baseado em vCore.](service-tiers-vcore.md) 

- O [modelo de compra baseado em DTU](service-tiers-dtu.md) oferece uma mistura de recursos de computação, memória e E/S em três níveis de serviço para suportar cargas de dados leves a pesos pesados: Básico, Standard e Premium. Os níveis de desempenho em cada camada fornecem uma mistura diferentes destes recursos, à qual pode adicionar recursos de armazenamento adicionais.
- O [modelo de compra baseado em vCore permite-lhe](service-tiers-vcore.md) escolher o número de vCores, a quantidade ou memória, e a quantidade e velocidade de armazenamento. Este modelo de compra oferece três níveis de serviço: Final geral, Business Critical e Hyperscale.

Pode construir a sua primeira aplicação numa base de dados pequena e única a um custo baixo por mês no nível de serviço Basic, Standard ou General Purpose e, em seguida, alterar o seu nível de serviço manualmente ou programáticamente a qualquer momento para o nível de serviço Premium ou Business Critical para atender às necessidades da sua solução. Pode ajustar o desempenho sem provocar tempos de inatividade na aplicação ou nos clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. A autoescala é quando uma balança de serviço se baseia automaticamente em critérios, enquanto a escalabilidade dinâmica permite a escala manual com um tempo de paragem mínimo.

Bases de dados únicas na Base de Dados Azure SQL suportam escalabilidade dinâmica manual, mas não autoescalada. Para uma experiência mais *automática*, considere utilizar conjuntos elásticos, que permitem que as bases de dados partilhem recursos num conjunto com base nas necessidades individuais da base de dados.
No entanto, existem scripts que podem ajudar a automatizar a escalabilidade de uma única base de dados na Base de Dados Azure SQL. Por exemplo, veja [Utilizar o PowerShell para monitorizar e dimensionar uma Base de Dados SQL única](scripts/monitor-and-scale-database-powershell.md).

Pode alterar os [escalões de serviço DTU](service-tiers-dtu.md) ou as [caraterísticas vCore](resource-limits-vcore-single-databases.md) a qualquer altura, com um período mínimo de indisponibilidade para a aplicação (em média, menos de quatro segundos). Para muitas empresas e aplicações, ser capaz de criar bases de dados e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Para este cenário, você usa uma piscina elástica com um certo número de eDTUs que são partilhados entre várias bases de dados na piscina.

![Introdução à Base de Dados SQL: DTUs de base de dados individual por camada e nível](./media/scale-resources/single_db_dtus.png)

A Azure SQL Database oferece a capacidade de escalar dinamicamente as suas bases de dados:

- Com uma [única base de dados,](single-database-scale.md)pode utilizar os modelos [DTU](resource-limits-dtu-single-databases.md) ou [vCore](resource-limits-vcore-single-databases.md) para definir a quantidade máxima de recursos que serão atribuídos a cada base de dados.
- [Piscinas elásticas](elastic-pool-scale.md) permitem definir o limite máximo de recursos por grupo de bases de dados na piscina.

Azure SQL Managed Instance permite-lhe escalar também: 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) usa o modo [vCores](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) e permite-lhe definir os núcleos máximos de CPU e o máximo de armazenamento atribuído ao seu exemplo. Todas as bases de dados dentro da instância gerida partilharão os recursos atribuídos à ocorrência.

Iniciar a escala ou escalar a ação em qualquer um dos sabores reiniciaria o processo do motor da base de dados e movê-lo-ia para uma máquina virtual diferente, se necessário. Mover o processo do motor da base de dados para uma nova máquina virtual é **um processo online** onde pode continuar a utilizar o serviço de Base de Dados Azure SQL existente enquanto o processo está em curso. Uma vez que o motor de base de dados-alvo esteja totalmente inicializado e pronto para processar as consultas, as ligações serão [mudadas de fonte para motor de base de dados-alvo](single-database-scale.md#impact).

> [!NOTE]
> Pode esperar uma breve rutura de ligação quando o processo de escala/escala estiver terminado. Se implementou [a lógica Retry para erros transitórios padrão,](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)não notará o failover.

## <a name="alternative-scale-methods"></a>Métodos de escala alternativa

A escala de recursos é a forma mais fácil e eficaz de melhorar o desempenho da sua base de dados sem alterar a base de dados ou o código de aplicação. Em alguns casos, mesmo os mais altos níveis de serviço, tamanhos de cálculo e otimizações de desempenho podem não lidar com a sua carga de trabalho de uma forma bem sucedida e rentável. Nesse caso, tem estas opções adicionais para escalar a sua base de dados:

- [A escala de leitura](read-scale-out.md) é uma funcionalidade disponível onde está a obter uma réplica apenas de leitura dos seus dados onde pode executar consultas exigentes apenas de leitura, como relatórios. Uma réplica só de leitura irá lidar com a sua carga de trabalho apenas de leitura sem afetar o uso de recursos na sua base de dados primária.
- [O fragmento de base de dados](elastic-scale-introduction.md) é um conjunto de técnicas que lhe permite dividir os seus dados em várias bases de dados e escaloná-los de forma independente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a melhoria do desempenho da base de dados alterando o código da base de [dados,](database-advisor-find-recommendations-portal.md)consulte Localizar e aplicar recomendações de desempenho .
- Para obter informações sobre a informação de uma base de dados incorporada que otimize a sua base de dados, consulte [a sintonização automática](automatic-tuning-overview.md).
- Para obter informações sobre a escala de leitura na Base de Dados Azure SQL, consulte como [utilizar réplicas apenas de leitura para carregar cargas de trabalho de consulta apenas de leitura de equilíbrio](read-scale-out.md).
- Para obter informações sobre um fragmento de base de dados, consulte [a Escala com base de dados Azure SQL](elastic-scale-introduction.md).
