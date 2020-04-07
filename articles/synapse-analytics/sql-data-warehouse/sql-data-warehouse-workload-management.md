---
title: Gestão de cargas de trabalho
description: Orientação para a implementação da gestão da carga de trabalho na Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744231"
---
# <a name="what-is-workload-management"></a>O que é a gestão da carga de trabalho?

A execução de cargas de trabalho mistas pode colocar desafios de recursos em sistemas ocupados.  A Solution Architects procura formas de separar as atividades clássicas de armazenamento de dados (como carregar, transformar e consultar dados) para garantir que existem recursos suficientes para atingir as SLAs.  

O isolamento físico do servidor pode levar a bolsas de infraestruturas que são subutilizadas, sobrelotadas ou num estado em que os caches estão constantemente a ser preparados com o arranque e paragem de hardware.  Um sistema de gestão de carga de trabalho bem sucedido gere eficazmente os recursos, garante uma utilização de recursos altamente eficiente e maximiza o retorno do investimento (ROI).

Uma carga de trabalho de armazém de dados refere-se a todas as operações que transpiram em relação a um armazém de dados. A profundidade e amplitude destes componentes dependem do nível de maturidade do armazém de dados.  A carga de trabalho do armazém de dados abrange:

- Todo o processo de carregamento de dados no armazém
- Realização de análises e relatórios de armazéns de dados
- Gestão de dados no armazém de dados
- Dados de exportação do armazém de dados

A capacidade de desempenho de um armazém de dados é determinada pelas unidades de armazém de [dados.](what-is-a-data-warehouse-unit-dwu-cdwu.md)

- Para ver os recursos atribuídos a todos os perfis de desempenho, consulte [os limites de Memória e concurrency](memory-concurrency-limits.md).
- Para ajustar a capacidade, pode [escalar para cima ou para baixo](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Conceitos de gestão da carga de trabalho

No passado, para a SQL Analytics em Azure Synapse geriste o desempenho da consulta através de aulas de [recursos.](resource-classes-for-workload-management.md)  As classes de recursos permitidas para atribuir memória a uma consulta baseada na adesão ao papel.  O principal desafio com as classes de recursos é que, uma vez configurados, não havia governação ou capacidade de controlar a carga de trabalho.  

Por exemplo, a concessão de uma adesão ao utilizador ad-hoc ao smallrc permitiu que esse utilizador consumisse 100% da memória no sistema.  Com as classes de recursos, não há como reservar e garantir que os recursos estão disponíveis para cargas de trabalho críticas.

A gestão da carga de trabalho do conjunto Synapse SQL em Azure Synapse consiste em três conceitos de alto nível: Classificação da Carga de [Trabalho,](sql-data-warehouse-workload-classification.md) [Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md) e Isolamento da Carga de [Trabalho.](sql-data-warehouse-workload-isolation.md)  Estas capacidades dão-lhe mais controlo sobre a forma como a sua carga de trabalho utiliza os recursos do sistema.

A classificação da carga de trabalho é o conceito de atribuir um pedido a um grupo de carga de trabalho e definir níveis de importância.  Historicamente, esta atribuição foi feita através de adesão a [papéis](resource-classes-for-workload-management.md#change-a-users-resource-class)usando sp_addrolemember .  Isto pode agora ser feito através do [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  A capacidade de classificação fornece um conjunto mais rico de opções, tais como etiqueta, sessão e tempo para classificar pedidos.

A importância da carga de trabalho influencia a ordem em que um pedido tem acesso aos recursos.  Num sistema movimentado, um pedido com maior importância tem primeiro acesso aos recursos.  A importância também pode garantir o acesso ordenado aos cadeados.

O isolamento da carga de trabalho reserva recursos para um grupo de carga de trabalho.  Os recursos reservados num grupo de carga de trabalho são mantidos exclusivamente para esse grupo de carga de trabalho para garantir a execução.  Os grupos de carga de trabalho também permitem definir a quantidade de recursos que são atribuídos por pedido, tal como as classes de recursos fazem.  Os grupos de carga de trabalho dão-lhe a capacidade de reservar ou limitar a quantidade de recursos que um conjunto de pedidos pode consumir.  Finalmente, os grupos de carga de trabalho são um mecanismo para aplicar regras, como o tempo de consulta, aos pedidos.  

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a classificação da carga de trabalho, consulte [a Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md).  
- Para obter mais informações sobre o isolamento da carga de trabalho, consulte [o Isolamento da Carga de Trabalho.](sql-data-warehouse-workload-isolation.md)  
- Para mais informações sobre a importância da carga de trabalho, consulte [a Importância da Carga de Trabalho.](sql-data-warehouse-workload-importance.md)  
- Para obter mais informações sobre a monitorização da gestão da carga de trabalho, consulte a Monitorização do Portal de [Gestão da Carga de Trabalho.](sql-data-warehouse-workload-management-portal-monitor.md)  
