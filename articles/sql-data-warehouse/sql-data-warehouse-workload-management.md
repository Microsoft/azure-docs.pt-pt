---
title: Gestão de cargas de trabalhos
description: Diretrizes para implementar o gerenciamento de carga de trabalho no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 66edde9591d4491fa630772f99372f9901319b2f
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940585"
---
# <a name="what-is-workload-management"></a>O que é o gerenciamento de carga de trabalho?

A execução de cargas de trabalho mistas pode representar desafios de recursos em sistemas ocupados.  Os arquitetos de soluções buscam maneiras de separar as atividades clássicas de data warehouse (como carregar, transformar e consultar dados) para garantir que existam recursos suficientes para atingir os SLAs.  

O isolamento de servidor físico pode levar a bolsos de infraestrutura que são subutilizadas, supercarregadas ou em um estado em que os caches estão constantemente sendo orientados com o início e a interrupção do hardware.  Um esquema de gerenciamento de carga de trabalho bem-sucedido gerencia efetivamente os recursos, garante uma utilização de recursos altamente eficiente e maximiza o retorno do investimento (ROI).

Uma carga de trabalho data warehouse refere-se a todas as operações que transtentam em relação a uma data warehouse. A profundidade e a amplitude desses componentes dependem do nível de maturidade do data warehouse.  A carga de trabalho data warehouse abrange: 
- Todo o processo de carregamento de dados no warehouse 
- Executando análise de data warehouse e relatórios
- Gerenciando dados no data warehouse 
- Exportando dados do data warehouse

A capacidade de desempenho de um data warehouse é determinada pelas [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Para exibir os recursos alocados para todos os perfis de desempenho, consulte [limites de memória e simultaneidade] memória-Concurrency-limits.md).
- Para ajustar a capacidade, você pode [escalar ou reduzir verticalmente](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Conceitos de gerenciamento de carga de trabalho
No passado, você gerenciou o desempenho da consulta em SQL Data Warehouse por meio de [classes de recurso](resource-classes-for-workload-management.md).  Classes de recursos permitidas para atribuir memória a uma consulta com base na associação de função.  O principal desafio com as classes de recursos é que, uma vez configurada, não havia nenhuma governança nem capacidade de controlar a carga de trabalho.  

Por exemplo, conceder uma associação de função de usuário ad hoc ao smallrc permitiu que o usuário consumisse 100% da memória no sistema.  Com as classes de recursos, não há como reservar e garantir que os recursos estejam disponíveis para cargas de trabalho críticas.

O gerenciamento de carga de trabalho em SQL Data Warehouse consiste em três conceitos de alto nível: [classificação de carga](sql-data-warehouse-workload-classification.md)de trabalho, [importância da carga](sql-data-warehouse-workload-importance.md) de trabalho e [isolamento](sql-data-warehouse-workload-isolation.md)  Esses recursos oferecem mais controle sobre como sua carga de trabalho utiliza os recursos do sistema.

Classificação de carga de trabalho é o conceito de atribuir uma solicitação a um grupo de carga de trabalho e definir níveis de importância.  Historicamente, essa atribuição foi feita por meio de associação de função usando [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Agora, isso pode ser feito por meio da [CLASSIFER criar carga de trabalho](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  O recurso de classificação fornece um conjunto mais rico de opções, como rótulo, sessão e tempo para classificar solicitações.

A importância da carga de trabalho influencia a ordem em que uma solicitação obtém acesso aos recursos.  Em um sistema ocupado, uma solicitação com maior importância tem o primeiro acesso aos recursos.  A importância também pode garantir o acesso ordenado a bloqueios. 

O isolamento da carga de trabalho reserva recursos para um grupo de cargas de trabalho.  Os recursos reservados em um grupo de carga de trabalho são mantidos exclusivamente para esse grupo de carga de trabalho para garantir a execução.  Os grupos de cargas de trabalho também permitem que você defina a quantidade de recursos atribuídos por solicitação, assim como as classes de recursos.  Os grupos de cargas de trabalho oferecem a capacidade de reservar ou limitar a quantidade de recursos que um conjunto de solicitações pode consumir.  Por fim, os grupos de cargas de trabalho são um mecanismo para aplicar regras, como tempo limite de consulta, a solicitações.  


## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a classificação de carga de trabalho, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md).  
- Para obter mais informações sobre isolamento de carga de trabalho, consulte [isolamento de carga de trabalho](sql-data-warehouse-workload-isolation.md).  
- Para obter mais informações sobre importância da carga de trabalho, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md).  
- Para obter mais informações sobre monitoramento de gerenciamento de carga de trabalho, consulte [monitoramento de carga de trabalho portal de gerenciamento](sql-data-warehouse-workload-management-portal-monitor.md).  