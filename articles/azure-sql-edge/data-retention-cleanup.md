---
title: Gerir dados históricos com política de retenção - Azure SQL Edge
description: Saiba como gerir dados históricos com a política de retenção em Azure SQL Edge
keywords: SQL Edge, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 45ce874ffb626f63b2239c66afdefd091114cbd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888133"
---
# <a name="manage-historical-data-with-retention-policy"></a>Gerir dados históricos com política de retenção

A Conservação de Dados pode ser ativada na base de dados e em qualquer uma das tabelas subjacentes individualmente, permitindo aos utilizadores criar políticas de envelhecimento flexível para as suas tabelas e bases de dados. A aplicação da retenção de dados é simples: requer apenas um parâmetro a ser definido durante a criação da tabela ou como parte de uma operação de mesa alterada. 

Após a política de retenção de dados ser desfiada para uma base de dados e para a tabela subjacente, uma tarefa de temporizador de fundo executa para remover quaisquer registos obsoletos da tabela ativada para a retenção de dados. A identificação das linhas correspondentes e a sua remoção da tabela ocorrem de forma transparente, na tarefa de fundo que é programada e executada pelo sistema. A condição de idade para as linhas de mesa é verificada com base na coluna utilizada como a `filter_column` definição da tabela. Se o período de retenção, por exemplo, for fixado para uma semana, as filas de mesa elegíveis para limpeza satisfazem uma das seguintes condições: 

- Se a coluna do filtro utilizar o tipo de dados DATETIMEOFFSET, então a condição é `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- Caso contrário, a condição é `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Fases de limpeza da conservação de dados

A operação de limpeza da conservação de dados é composta por duas fases. 
- Fase descoberta - Nesta fase, a operação de limpeza identifica todas as tabelas dentro das bases de dados dos utilizadores para construir uma lista para limpeza. A descoberta corre uma vez por dia.
- Fase de Limpeza - Nesta fase, a limpeza é feita contra todas as tabelas com retenção finita de dados, identificadas na fase de descoberta. Se a operação de limpeza não puder ser efetuada numa mesa, então essa tabela é ignorada na corrente e será novamente julgada na próxima iteração. Os seguintes princípios são usados durante a limpeza
    - Se uma linha obsoleta for bloqueada por outra transação, essa linha é ignorada. 
    - A limpeza funciona com uma definição de tempo de bloqueio de 5 segundos padrão. Se as fechaduras não puderem ser adquiridas nas tabelas dentro da janela de intervalo, a mesa é ignorada na corrente e será novamente julgada na próxima iteração.
    - Se houver um erro durante a limpeza de uma mesa, esta tabela é ignorada e será captada na próxima iteração.

## <a name="manual-cleanup"></a>Limpeza manual

Dependendo das definições de retenção de dados numa tabela e da natureza da carga de trabalho na base de dados, é possível que o fio de limpeza automático não remova completamente todas as linhas obsoletas durante o seu funcionaamento. Para ajudar e permitir que os utilizadores removam manualmente as linhas obsoletas, o `sys.sp_cleanup_data_retention` procedimento armazenado foi introduzido no Azure SQL Edge. 

Este procedimento armazenado requer três parâmetros. 
    - Nome do esquema - Nome do próprio esquema para a mesa. Este é um parâmetro necessário. 
    - Nome da mesa - Nome da tabela para a qual está a ser executada a limpeza manual. Este é um parâmetro necessário. 
    - rowcount (Output) - variável de saída. Devolve o número de linhas limpas pela limpeza manual sp. Este é um parâmetro opcional. 

O exemplo a seguir mostra a execução da limpeza manual sp para a tabela `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Como as linhas obsoletas são eliminadas

O processo de limpeza depende da disposição do índice da tabela. Uma tarefa de fundo é criada para executar a limpeza de dados obsoleta para todas as tabelas com período de retenção finito. A lógica de limpeza para o índice rowstore (árvore B ou Heap) elimina a linha envelhecida em pedaços menores (até 10K) minimizando a pressão no registo de base de dados e no subsistema IO. Embora a lógica de limpeza utilize o índice de árvore B necessário, a ordem de supressões para as linhas mais antigas do que o período de retenção não pode ser firmemente garantida. Por isso, não tome qualquer dependência da ordem de limpeza nas suas aplicações.

A tarefa de limpeza da loja de colunas agrupadas remove grupos de linha inteiros ao mesmo tempo (normalmente contêm 1 milhão de linhas cada), o que é muito eficiente, especialmente quando os dados são gerados e envelhece a um ritmo elevado.

![Limpeza de Retenção de Dados](./media/data-retention-cleanup/data-retention-cleanup.png)

Uma excelente compressão de dados e uma limpeza eficiente da retenção fazem do índice de colunas agrupados uma escolha perfeita para cenários quando a sua carga de trabalho gera rapidamente uma elevada quantidade de dados.

> [!Note]
> No caso dos Índices b-tree e dos montes, a retenção de dados executa uma consulta de eliminação nas tabelas subjacentes, o que pode entrar em conflito com os gatilhos de eliminação nas tabelas. Recomenda-se remover os gatilhos das tabelas ou não permitir a retenção de dados em tabelas que tenham o gatilho de DML.

## <a name="monitoring-data-retention-cleanup"></a>Monitorização da limpeza da conservação de dados

As operações de limpeza da política de retenção de dados podem ser monitorizadas através de eventos alargados (XEvents) em Azure SQL Edge. Para obter mais informações sobre eventos alargados, consulte [a Visão Geral do XEvents](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events). 

Os seis eventos prolongados seguintes ajudam a acompanhar o estado das operações de limpeza. 

| Nome | Descrição |
|------| ------------|
| data_retention_task_started  | Ocorre quando a tarefa de fundo para limpeza de tabelas com política de retenção começa. |
| data_retention_task_completed  | Ocorre quando termina a tarefa de fundo para a limpeza de tabelas com política de retenção. |
| data_retention_task_exception  | Ocorre quando a tarefa de fundo para limpeza de tabelas com política de retenção falha fora do processo de limpeza de retenção específico para a mesa. |
| data_retention_cleanup_started  | Ocorre quando o processo de limpeza da tabela com a política de retenção de dados começa. |
| data_retention_cleanup_exception  | Ocorre o processo de limpeza da tabela com falha na política de retenção. |
| data_retention_cleanup_completed  | Ocorre quando o processo de limpeza da tabela com a política de retenção de dados termina. |  

Adicionalmente, um novo tipo de tampão de anel nomeado `RING_BUFFER_DATA_RETENTION_CLEANUP` foi adicionado à sys.dm_os_ring_buffers vista dinâmica de gestão. Esta visão pode ser usada para monitorizar as operações de limpeza da conservação de dados. 


## <a name="next-steps"></a>Passos Seguintes
- [Política de Retenção de Dados](data-retention-overview.md)
- [Ativar e desativar políticas de retenção de dados](data-retention-enable-disable.md)
