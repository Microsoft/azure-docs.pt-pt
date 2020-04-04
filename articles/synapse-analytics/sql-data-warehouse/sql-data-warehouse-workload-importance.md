---
title: Importância da carga de trabalho
description: Orientação para definir importância para consultas de piscina Synapse SQL em Azure Synapse Analytics.
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
ms.openlocfilehash: 2c8617cffaa81da6423011a494b8dbc82c42d218
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632462"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Importância da carga de trabalho da Azure Synapse Analytics

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução para pedidos de piscina Synapse SQL em Azure Synapse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

As necessidades empresariais podem exigir que as cargas de trabalho de armazenamento de dados sejam mais importantes do que outras.  Considere um cenário em que os dados críticos de vendas da missão são carregados antes do fecho do período fiscal.  As cargas de dados para outras fontes, como os dados meteorológicos, não têm SLAs rigorosos. A definição de alta importância para um pedido de carga de dados de vendas e baixa importância para um pedido de carga de dados meteorológicos garante que a carga de dados de vendas obtém primeiro acesso aos recursos e completa mais rapidamente.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixo, below_normal, normal, above_normal e alto.  Pedidos que não derem importância são atribuídos ao nível padrão do normal. Os pedidos que têm o mesmo nível de importância têm o mesmo comportamento de agendamento que existe hoje.

## <a name="importance-scenarios"></a>Cenários de importância

Para além do cenário de importância básica acima descrito com os dados de vendas e meteorologia, existem outros cenários em que a importância da carga de trabalho ajuda a satisfazer as necessidades de processamento e consulta de dados.

### <a name="locking"></a>Bloquear

O acesso a fechaduras para atividade de leitura e escrita é uma área de contenção natural. Atividades como [a troca de divisórias](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [o OBJETO DE MUDANÇA RENAME](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) requerem fechaduras elevadas.  Sem importância de carga de trabalho, a piscina SQL Synapse em Azure Synapse otimiza para a entrada. Otimizar para a entrada significa que quando os pedidos de execução e fila têm as mesmas necessidades de bloqueio e os recursos estão disponíveis, os pedidos em fila podem contornar pedidos com necessidades de bloqueio mais elevadas que chegaram na fila de pedidos mais cedo. Uma vez aplicada a importância da carga de trabalho a pedidos com necessidades de bloqueio mais elevadas. O pedido com maior importância será feito antes do pedido com menor importância.

Considere o exemplo seguinte:

- O Q1 está a executar e a selecionar dados ativamente do SalesFact.
- O Q2 está na fila à espera que a Q1 esteja concluída.  Foi submetida às 9h e está a tentar dividir a troca de novos dados para SalesFact.
- O Q3 é submetido às 9h01 e quer selecionar dados do SalesFact.

Se o Q2 e o Q3 tiverem a mesma importância e o Q1 ainda estiver a ser executado, o Q3 começará a ser executado. O Q2 continuará à espera de um bloqueio exclusivo no SalesFact.  Se o Q2 tiver maior importância do que o Q3, o Q3 esperará até que o Q2 esteja terminado antes de poder começar a execução.

### <a name="non-uniform-requests"></a>Pedidos não uniformes

Outro cenário em que a importância pode ajudar a satisfazer as exigências de consulta é quando são apresentados pedidos com diferentes classes de recursos.  Como já foi referido, com a mesma importância, a piscina SQL Synapse em Azure Synapse otimiza para a entrada. Quando os pedidos de tamanho misto (como smallrc ou mediumrc) são em fila, a piscina SYnapse SQL escolherá o pedido de chegada mais antigo que se enquadre nos recursos disponíveis. Se for aplicada a importância da carga de trabalho, o pedido de maior importância é agendado em seguida.
  
Considere o seguinte exemplo sobre DW500c:

- Q1, Q2, Q3 e Q4 estão executando consultas de pequenos rc.
- O Q5 é submetido com a classe de recursos médios às 9h.
- O Q6 é submetido com classe de recursos smallrc às 9:01 da manhã.

Como o Q5 é médio, requer duas ranhuras de condivisões. O Q5 precisa de esperar que duas das consultas de corrida completem.  No entanto, quando uma das consultas de execução (Q1-Q4) termina, o Q6 é agendado imediatamente porque os recursos existem para executar a consulta.  Se o Q5 tem maior importância do que o Q6, o Q6 espera até que o Q5 esteja a decorrer antes de começar a executar.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a criação de um classificador, consulte a [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Para obter mais informações sobre a classificação da carga de trabalho, consulte [a Classificação da Carga de Trabalho](sql-data-warehouse-workload-classification.md).  
- Consulte o [Quickstart Create workload classifier](quickstart-create-a-workload-classifier-tsql.md) para como criar um classificador de carga de trabalho.
- Consulte os artigos de como [configurar](sql-data-warehouse-how-to-configure-workload-importance.md) a importância da carga de trabalho e como gerir e monitorizar a Gestão da [Carga de Trabalho.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Veja [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) dm_pdw_exec_requests para ver as consultas e a importância atribuída.
