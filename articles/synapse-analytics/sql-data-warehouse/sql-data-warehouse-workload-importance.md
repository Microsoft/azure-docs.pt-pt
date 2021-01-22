---
title: Importância da carga de trabalho
description: Orientação para definir importância para consultas dedicadas de piscina SQL em Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678411"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics importância da carga de trabalho

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução para pedidos dedicados de piscina SQL em Azure Synapse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

As necessidades empresariais podem exigir que as cargas de trabalho de armazenamento de dados sejam mais importantes do que outras.  Considere um cenário em que os dados críticos de vendas da missão são carregados antes do fecho do período fiscal.  As cargas de dados para outras fontes, como os dados meteorológicos, não têm SLAs rígidos. Definir uma grande importância para um pedido de carregamento de dados de vendas e baixa importância para um pedido de carga de dados meteorológicos garante que a carga de dados de vendas obtém primeiro acesso aos recursos e completa mais rapidamente.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixo, below_normal, normal, above_normal e alto.  Pedidos que não definem importância são atribuídos o nível padrão do normal. Os pedidos que têm o mesmo nível de importância têm o mesmo comportamento de agendamento que existe hoje.

## <a name="importance-scenarios"></a>Cenários de importância

Para além do cenário de importância básica acima descrito com dados de vendas e meteorologia, existem outros cenários em que a importância da carga de trabalho ajuda a satisfazer as necessidades de processamento e consulta de dados.

### <a name="locking"></a>Bloquear

O acesso a fechaduras para atividade de leitura e escrita é uma área de contenção natural. Atividades como [comutação de divisórias](sql-data-warehouse-tables-partition.md) ou [OBJECT RENAME](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) requerem fechaduras elevadas.  Sem importância de carga de trabalho, a piscina SQL dedicada em Azure Synapse otimiza para produção. Otimizar para a produção significa que quando os pedidos de execução e fila têm as mesmas necessidades de bloqueio e recursos estão disponíveis, os pedidos em fila podem contornar pedidos com necessidades de bloqueio mais elevadas que chegaram na fila de pedidos mais cedo. Uma vez que a importância da carga de trabalho é aplicada a pedidos com necessidades de bloqueio mais elevadas. O pedido com maior importância será executado antes do pedido com menor importância.

Considere o exemplo seguinte:

- O Q1 está a executar e selecionar ativamente dados do SalesFact.
- Q2 está na fila à espera que a Q1 esteja completa.  Foi submetida às 9h e está a tentar trocar novos dados para o SalesFact.
- O Q3 é submetido às 9h01 e quer selecionar dados do SalesFact.

Se o Q2 e o Q3 tiverem a mesma importância e o Q1 ainda estiver a ser executado, o Q3 começará a ser executado. O Q2 continuará a aguardar um bloqueio exclusivo no SalesFact.  Se o Q2 tiver maior importância do que o Q3, o Q3 esperará até que o Q2 esteja terminado antes de poder iniciar a execução.

### <a name="non-uniform-requests"></a>Pedidos não uniformes

Outro cenário em que a importância pode ajudar a satisfazer as exigências de consulta é quando são apresentados pedidos com diferentes classes de recursos.  Como já foi mencionado, sob a mesma importância, a piscina SQL dedicada em Azure Synapse otimiza para a produção. Quando os pedidos de tamanho misto (como o smallrc ou o mediumrc) forem em fila, a piscina de SQL dedicada escolherá o pedido de chegada mais precoce que se enquadra nos recursos disponíveis. Se a importância da carga de trabalho for aplicada, o pedido de maior importância é agendado a seguir.
  
Considere o seguinte exemplo no DW500c:

- Q1, Q2, Q3 e Q4 estão a fazer consultas de pequeno porção.
- O Q5 é submetido com a classe de recursos mediumrc às 9h.
- O Q6 é submetido com classe de recursos smallrc às 9:01 da manhã.

Como o Q5 é médio, requer duas ranhuras de concordância. O Q5 precisa esperar que duas das consultas de corrida completem.  No entanto, quando uma das consultas em execução (Q1-Q4) termina, o Q6 é agendado imediatamente porque existem recursos para executar a consulta.  Se o Q5 tiver maior importância do que o Q6, o Q6 espera até que o Q5 esteja em execução antes de poder começar a executar.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a criação de um classificador, consulte o [CLASSIFIER CREATE WORKLOAD (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Para obter mais informações sobre a classificação da carga de trabalho, consulte [a Classificação da Carga de Trabalho.](sql-data-warehouse-workload-classification.md)  
- Consulte o classificador Quickstart [Criar carga de trabalho](quickstart-create-a-workload-classifier-tsql.md) para criar um classificador de carga de trabalho.
- Consulte os artigos de como configurar a [importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como gerir e monitorizar a [Gestão da Carga de Trabalho.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para visualizar as consultas e a importância atribuída.
