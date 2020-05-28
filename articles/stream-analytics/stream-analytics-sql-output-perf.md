---
title: Saída do Azure Stream Analytics para a Base de Dados Azure SQL
description: Aprenda sobre a colocação de dados para o SQL Azure do Azure Stream Analytics e obtenha taxas de visualização de escrita mais elevadas.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 3d166c8fd893f38d587dbeff1d86530c46f89630
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018791"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Saída do Azure Stream Analytics para a Base de Dados Azure SQL

Este artigo discute dicas para obter um melhor desempenho de escrita de desempenho de desempenho quando está a carregar dados para a Base de Dados SQL Azure usando o Azure Stream Analytics.

A saída sQL no Azure Stream Analytics suporta a escrita paralelamente como uma opção. Esta opção permite topologas de trabalho [totalmente paralelas,](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) onde várias divisórias de saída estão escrevendo para a tabela de destino em paralelo. No entanto, permitir esta opção no Azure Stream Analytics pode não ser suficiente para obter maiores resultados, uma vez que depende significativamente da configuração da base de dados SQL Azure e do esquema de mesa. A escolha dos índices, a chave de agrupamento, o fator de preenchimento do índice e a compressão têm impacto no tempo de carregar tabelas. Para obter mais informações sobre como otimizar a sua base de dados SQL Azure para melhorar o desempenho da consulta e da carga com base em referências internas, consulte a orientação de desempenho da base de [dados SQL](../azure-sql/database/performance-guidance.md). A encomenda de escritos não é garantida quando se escreve em paralelo com a Base de Dados SQL Azure.

Aqui estão algumas configurações dentro de cada serviço que podem ajudar a melhorar a entrada geral da sua solução.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Divisão Herdada** – Esta opção de configuração de saída SQL permite herdar o esquema de partição do seu passo ou entrada de consulta anterior. Com isto habilitado, escrever para uma tabela baseada em disco e ter uma topologia [totalmente paralela](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) para o seu trabalho, espere ver melhores resultados. Esta divisão já acontece automaticamente para muitas [outras saídas](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). O bloqueio de mesa (TABLOCK) também é desativado para inserções a granel feitas com esta opção.

> [!NOTE] 
> Quando existam mais de 8 divisórias de entrada, herdar o regime de partição de entrada pode não ser uma escolha adequada. Este limite superior foi observado numa tabela com uma única coluna de identidade e um índice agrupado. Neste caso, considere usar [o INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 na sua consulta, para especificar explicitamente o número de autores de saída. Com base no seu esquema e escolha de índices, as suas observações podem variar.

- **Tamanho do lote** - A configuração de saída SQL permite especificar o tamanho máximo do lote numa saída SQL Azure Stream Analytics com base na natureza da sua tabela/carga de trabalho de destino. O tamanho do lote é o número máximo de registos que enviados com cada transação de inserção a granel. Nos índices de lojas de colunas agrupadas, os tamanhos do lote em torno de [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permitem mais paralelização, registo mínimo e otimizações de bloqueio. Nas tabelas baseadas em disco, 10K (predefinido) ou inferior pode ser o ideal para a sua solução, uma vez que tamanhos mais elevados do lote podem desencadear uma escalada de bloqueio durante as inserções a granel.

- **Afinação** de mensagens de entrada – Se tiver otimizado usando divisórias herdadas e tamanho de lote, aumentar o número de eventos de entrada por mensagem por divisória ajuda a aumentar ainda mais a sua produção de escrita. A sintonização da mensagem de entrada permite que os tamanhos do lote dentro do Azure Stream Analytics estejam até ao tamanho do lote especificado, melhorando assim a produção. Isto pode ser conseguido usando [a compressão](stream-analytics-define-inputs.md) ou aumentando o tamanho da mensagem de entrada em EventHub ou Blob.

## <a name="sql-azure"></a>SQL Azure

- **Tabela e Índices divididos** – A utilização de uma tabela SQL [dividida](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) e de índices divididos na tabela com a mesma coluna que a sua chave de partição (por exemplo, PartitionId) pode reduzir significativamente as disputas entre divisórias durante as escritas. Para uma tabela dividida, terá de criar uma função de [partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) e um esquema de [partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) no grupo de arquivos PRIMARY. Isto também aumentará a disponibilidade de dados existentes enquanto novos dados estão a ser carregados. O limite de Log IO pode ser atingido com base no número de divisórias, que podem ser aumentadas através da atualização do SKU.

- **Evite violações de chaves únicas** – Se receber [várias mensagens](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) de aviso de violação chave no Registo de Atividades do Azure Stream Analytics, certifique-se de que o seu trabalho não é afetado por violações de restrições únicas que são suscetíveis de ocorrer durante casos de recuperação. Isto pode ser evitado definindo a opção [IGNORE \_ DUP \_ KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) nos seus índices.

## <a name="azure-data-factory-and-in-memory-tables"></a>Fábrica de Dados Azure e Tabelas em Memória

- **Tabela in-Memory como tabela temporária** – [As tabelas in-Memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) permitem cargas de dados de alta velocidade, mas os dados precisam de se encaixar na memória. Os parâmetros de referência mostram que o carregamento a granel de uma tabela em memória para uma tabela baseada em disco é cerca de 10 vezes mais rápido do que inserir diretamente a granel usando um único escritor na tabela baseada em disco com uma coluna de identidade e um índice agrupado. Para alavancar este desempenho de inserção a granel, configurar um trabalho de cópia utilizando a [Azure Data Factory](../data-factory/connector-azure-sql-database.md) que copia os dados da tabela de memória para a tabela baseada em disco.

## <a name="avoiding-performance-pitfalls"></a>Evitar armadilhas de desempenho
A inserção a granel é muito mais rápida do que carregar dados com inserções individuais porque evita-se a sobrecarga repetida de transferir os dados, analisar a declaração de inserção, executar a declaração e emitir um registo de transações. Em vez disso, um caminho mais eficiente é usado no motor de armazenamento para transmitir os dados. O custo de configuração deste caminho é no entanto muito mais elevado do que uma única declaração de inserção numa tabela baseada em disco. O ponto de break-even é tipicamente cerca de 100 linhas, além das quais o carregamento a granel é quase sempre mais eficiente. 

Se a taxa de eventos de entrada for baixa, pode facilmente criar tamanhos de lote inferiores a 100 linhas, o que torna a inserção a granel ineficiente e utiliza demasiado espaço de disco. Para contornar esta limitação, pode fazer uma destas ações:
* Crie um [gatilho](/sql/t-sql/statements/create-trigger-transact-sql) em vez de utilizar uma inserção simples para cada linha.
* Utilize uma tabela temporária In-Memory, tal como descrito na secção anterior.

Outro cenário deste tipo ocorre quando se escreve num índice de lojas de colunas não agrupados (NCCI), onde inserções a granel mais pequenas podem criar demasiados segmentos, que podem colidir com o índice. Neste caso, a recomendação é utilizar um índice de Columnstore Clustered.

## <a name="summary"></a>Resumo

Em resumo, com a funcionalidade de saída dividida no Azure Stream Analytics para a saída de SQL, a paralelização alinhada do seu trabalho com uma tabela dividida no SQL Azure deve dar-lhe melhorias significativas de produção. A alavancagem da Fábrica de Dados Azure para orquestrar o movimento de dados de uma tabela In-Memory para tabelas baseadas em Disco pode dar ordem de ganhos de produção de magnitude. Se for possível, melhorar a densidade da mensagem também pode ser um fator importante para melhorar a sua capacidade de ser global.
