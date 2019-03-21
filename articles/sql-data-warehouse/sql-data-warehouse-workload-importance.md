---
title: Importância da carga de trabalho | Documentos da Microsoft
description: Documentação de orientação para a definição importância para consultas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: e53a6fcefb0f5370f6e24cc50fad2ad4ad4c64e3
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864098"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Importância de carga de trabalho do armazém de dados SQL (pré-visualização)

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução para pedidos do SQL Data Warehouse.

> [!Note]
> Importância da carga de trabalho está disponível no SQL Data Warehouse Gen2.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Necessidades de negócio podem necessitar de cargas de trabalho para ser mais importante do que outras pessoas de armazém de dados.  Considere um cenário em que os dados de vendas crítico de missão são carregados antes do fiscal período fechar.  Carregamento dos dados para outras origens, como os dados meteorológicos não tem strict SLAs.   A definição de importância alta de um pedido carregar dados de vendas e de baixa importância a um pedido de carga, independentemente de dados garante a carga de dados de vendas obtém primeiro acesso a recursos e conclusão mais rápida.

## <a name="importance-levels"></a>Níveis de importância

Existem cinco níveis de importância: baixa, below_normal, normal, above_normal e alta.  Pedidos que não defina a importância recebem o nível predefinido de normal.  Pedidos que tenham o mesmo nível de importância de ter o mesmo comportamento de agendamento que existe atualmente.

## <a name="importance-scenarios"></a>Cenários de importância

Para além do cenário de importância básico descrito acima, com vendas e dados meteorológicos, existem outros cenários em que a importância da carga de trabalho ajuda a atender e necessidades de consulta de processamento de dados.

### <a name="locking"></a>Bloqueio

Aceder aos bloqueios de leitura e escrita a atividade é uma área de contenção natural.  Atividades, tais como [alternância de partição](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [mudar o nome do OBJETO](/sql/t-sql/statements/rename-transact-sql) requerem elevados bloqueios.  Sem importância da carga de trabalho, o SQL Data Warehouse otimiza para débito.  Otimizar para o débito significa que, quando em execução e solicitações em fila têm as mesmas necessidades de bloqueios e os recursos estão disponíveis, os pedidos em fila podem ignorar pedidos com bloqueio superior às suas necessidades que chegaram na fila de pedido anteriormente.  Uma vez aplicada para pedidos com bloqueio de maior importância da carga de trabalho precisa. Pedido com maior importância será executado antes de pedido com importância inferior.

Considere o exemplo a seguir:

P1 está ativamente em execução e selecionar dados em SalesFact.
Q2 é colocada na fila esperar até que o Q1 concluir.  Ele foi submetido às 9h e está a tentar criar partições de dados novo comutador em SalesFact.
P3 é submetida às 9h: 01 e deseja selecionar dados da SalesFact.

Se P2 e P3 têm a mesma importância e Q1 ainda está em execução, P3 começará a execução. Q2 irá continuar a aguardar por um bloqueio exclusivo no SalesFact.  Se Q2 tem maior importância que P3, P3 aguardará até que Q2 seja concluída antes de iniciar execução.

### <a name="non-uniform-requests"></a>Pedidos não-uniforme

Outro cenário em que o importância pode ajudar a atender às demandas de consultas é quando são submetidos pedidos com classes de recursos diferentes.  Como foi mencionado anteriormente, sob a mesma importância, otimiza o SQL Data Warehouse para um débito.  Quando os pedidos de tamanho misto (como smallrc ou mediumrc) são colocados em fila, o SQL Data Warehouse irá escolher o pedido mais antiga que são recebido que enquadra-se os recursos disponíveis.  Se for aplicada a importância da carga de trabalho, o pedido de importância mais alto está agendado seguinte.
  
Considere o exemplo a seguir no DW500c:

P1, P2, P3 e P4 executem smallrc consultas.
Às 9h, P5 são enviados com a classe de recursos de mediumrc.
P6 é submetido com classe de recursos de smallrc às 9h: 01.

Como P5 é mediumrc, ele requer dois blocos de simultaneidade.  P5 tem de aguardar para duas das consultas em execução para concluir.  No entanto, quando tiver concluído das consultas em execução (P1 – P4), P6 está previsto que imediatamente porque os recursos existem para executar a consulta.  Se P5 tem maior importância que P6, P6 aguarda até que P5 está em execução antes de poder começar a executar.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a classificação de carga de trabalho do armazém de dados SQL, consulte [a classificação de carga de trabalho de armazém de dados SQL](sql-data-warehouse-workload-classification.md) e [criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md). Ver [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para ver as consultas e a importância atribuído.
