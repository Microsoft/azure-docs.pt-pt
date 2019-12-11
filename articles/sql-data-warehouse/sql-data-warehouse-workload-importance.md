---
title: Importância das cargas de trabalho
description: Diretrizes para definir a importância para consultas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28d239d47b46a5aafdf65c72ef826a0efb79f52b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974638"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Importância da carga de trabalho SQL Data Warehouse do Azure

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução de solicitações de SQL Data Warehouse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

As necessidades comerciais podem exigir que as cargas de trabalho de data warehousing sejam mais importantes do que outras.  Considere um cenário em que os dados de vendas de missão crítica são carregados antes do fechamento do período fiscal.  Cargas de dados para outras fontes, como dados de clima, não têm SLAs estritos. Definir a alta importância para uma solicitação para carregar dados de vendas e baixa importância para uma solicitação para carregar se os dados garantem que o carregamento dos dados de vendas receba o primeiro acesso aos recursos e seja concluído mais rapidamente.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixa, below_normal, normal, above_normal e alta.  As solicitações que não definem a importância são atribuídas ao nível padrão normal. As solicitações que têm o mesmo nível de importância têm o mesmo comportamento de agendamento que existe hoje.

## <a name="importance-scenarios"></a>Cenários de importância

Além do cenário de importância básica descrito acima com dados de vendas e clima, há outros cenários em que a importância da carga de trabalho ajuda a atender às necessidades de processamento e consulta de dados.

### <a name="locking"></a>Bloquear

O acesso a bloqueios para atividade de leitura e gravação é uma área de contenção natural. Atividades como [troca de partição](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [renomear objeto](/sql/t-sql/statements/rename-transact-sql) exigem bloqueios elevados.  Sem importância de carga de trabalho, o SQL Data Warehouse otimiza para a taxa de transferência.  Otimizar para taxa de transferência significa que, quando as solicitações em execução e em fila tiverem as mesmas necessidades de bloqueio e os recursos estiverem disponíveis, as solicitações enfileiradas poderão ignorar as solicitações com maiores necessidades de bloqueio que chegaram na fila de solicitações anteriormente.  Quando a importância da carga de trabalho é aplicada às solicitações com maiores necessidades de bloqueio, as solicitações com maior importância serão executadas antes da solicitação com menor importância.

Considere o exemplo seguinte:

Q1 está executando ativamente e selecionando dados de SalesFact.
O Q2 está na fila aguardando a conclusão de Q1.  Ele foi enviado às 9h e está tentando particionar novos dados no SalesFact.
Q3 é enviado em 9:01am e deseja selecionar dados de SalesFact.

Se Q2 e Q3 tiverem a mesma importância e Q1 ainda estiver em execução, Q3 começará a ser executado. O Q2 continuará aguardando um bloqueio exclusivo em SalesFact.  Se Q2 tiver maior importância do que Q3, Q3 aguardará até que Q2 seja concluído antes de iniciar a execução.

### <a name="non-uniform-requests"></a>Solicitações não uniformes

Outro cenário em que a importância pode ajudar a atender às demandas de consulta é quando as solicitações com diferentes classes de recursos são enviadas.  Como foi mencionado anteriormente, sob a mesma importância, SQL Data Warehouse otimiza para a taxa de transferência. Quando solicitações de tamanho misto (como smallrc ou mediumrc) são enfileiradas, SQL Data Warehouse escolherá a primeira solicitação de chegada que se ajusta aos recursos disponíveis. Se a importância da carga de trabalho for aplicada, a solicitação de maior importância será agendada a seguir.
  
Considere o seguinte exemplo em DW500c:

Q1, T2, Q3 e Q4 estão executando consultas smallrc.
P5 é enviado com a classe de recurso mediumrc às 9h.
P6 é enviado com a classe de recurso smallrc em 9:01am.

Como P5 é mediumrc, ele requer dois slots de simultaneidade. O P5 precisa aguardar a conclusão de duas consultas em execução.  No entanto, quando uma das consultas em execução (Q1-T4) for concluída, o P6 será agendado imediatamente porque os recursos existem para executar a consulta.  Se P5 tiver maior importância do que P6, P6 aguardará até que P5 esteja em execução antes que possa começar a execução.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como criar um classificador, consulte [criar classificação de carga de trabalho (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Para obter mais informações sobre a classificação de carga de trabalho SQL Data Warehouse, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md).  
- Consulte o [classificador início rápido criar carga de trabalho](quickstart-create-a-workload-classifier-tsql.md) para saber como criar um classificador de carga de trabalho.
- Consulte os artigos de instruções para [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consulte [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.
