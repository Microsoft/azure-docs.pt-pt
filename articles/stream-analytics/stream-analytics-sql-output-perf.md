---
title: Aumentar o desempenho da produção para a Base de Dados Azure SQL da Azure Stream Analytics
description: Saiba mais sobre a produção de dados para o SQL Azure da Azure Stream Analytics e obtenha taxas de produção de escrita mais elevadas.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: a902cbc8256e249a608a5da8436a1145ddb0d565
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490938"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Aumentar o desempenho da produção para a Base de Dados Azure SQL da Azure Stream Analytics

Este artigo discute dicas para obter um melhor desempenho de produção de escrita quando está a carregar dados na Base de Dados Azure SQL usando a Azure Stream Analytics.

A saída SQL em Azure Stream Analytics suporta a escrita em paralelo como uma opção. Esta opção permite topologias de trabalho [totalmente paralelas,](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) onde várias divisórias de saída estão escrevendo para a tabela de destino em paralelo. No entanto, permitir esta opção no Azure Stream Analytics pode não ser suficiente para obter posições mais elevadas, uma vez que depende significativamente da configuração da sua base de dados e do esquema de tabela. A escolha dos índices, a chave de agrupamento, o fator de preenchimento do índice e a compressão têm um impacto no tempo para carregar tabelas. Para obter mais informações sobre como otimizar a sua base de dados para melhorar a consulta e o desempenho da carga com base em referências internas, consulte [a orientação de desempenho da Base de Dados SQL](../azure-sql/database/performance-guidance.md). A encomenda de escritos não é garantida quando se escreve em paralelo com a Base de Dados SQL.

Aqui estão algumas configurações dentro de cada serviço que podem ajudar a melhorar o rendimento geral da sua solução.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Herdar partição** – Esta opção de configuração de saída SQL permite herdar o esquema de partição do seu passo ou entrada de consulta anterior. Com isto ativado, escrever para uma mesa baseada em disco e ter uma topologia [totalmente paralela](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) para o seu trabalho, espere ver melhores posições. Esta partição já acontece automaticamente para muitas [outras saídas](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). O bloqueio da mesa (TABLOCK) também é desativado para inserções a granel feitas com esta opção.

> [!NOTE] 
> Quando existem mais de 8 divisórias de entrada, herdar o sistema de partição de entrada pode não ser uma escolha adequada. Este limite superior foi observado numa tabela com uma única coluna de identidade e um índice agrupado. Neste caso, considere utilizar [o INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 na sua consulta, para especificar explicitamente o número de autores de saída. Com base no seu esquema e escolha de índices, as suas observações podem variar.

- **Tamanho do lote** - configuração de saída SQL permite especificar o tamanho máximo do lote numa saída SQL Azure Stream Analytics com base na natureza da sua tabela/carga de trabalho de destino. O tamanho do lote é o número máximo de registos que enviaram com cada transação de inserção a granel. Nos índices de lojas de colunas agrupadas, os tamanhos dos lotes em torno de [100K](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permitem uma maior paralelização, registo mínimo e otimizações de bloqueio. Nas tabelas baseadas em disco, 10K (padrão) ou inferior podem ser ideais para a sua solução, uma vez que tamanhos mais elevados do lote podem desencadear uma escalada de bloqueio durante as inserções a granel.

- **Sintonização de mensagem** de entrada – Se otimizado usando a partilha herdada e o tamanho do lote, aumentar o número de eventos de entrada por mensagem por partição ajuda a aumentar ainda mais a sua produção de escrita. A sintonização da mensagem de entrada permite que os tamanhos dos lotes dentro do Azure Stream Analytics estejam até ao tamanho especificado do lote, melhorando assim a produção. Isto pode ser conseguido usando [a compressão](stream-analytics-define-inputs.md) ou aumentando os tamanhos de mensagem de entrada no EventHub ou Blob.

## <a name="sql-azure"></a>SQL Azure

- **Tabela e Índices divididos** – A [utilização de](/sql/relational-databases/partitions/partitioned-tables-and-indexes) uma tabela SQL dividida e de índices divididos na tabela com a mesma coluna que a sua chave de partição (por exemplo, PartitionId) pode reduzir significativamente as disputas entre as divisórias durante as escritas. Para uma tabela dividida, terá de criar uma [função de partição](/sql/t-sql/statements/create-partition-function-transact-sql) e um [esquema de partição](/sql/t-sql/statements/create-partition-scheme-transact-sql) no grupo de ficheiros PRIMARY. Isto também aumentará a disponibilidade de dados existentes enquanto novos dados estão a ser carregados. O limite de IO de registo pode ser atingido com base no número de divisórias, que podem ser aumentadas através da atualização do SKU.

- **Evite violações chave únicas** – Se receber [várias mensagens de aviso de violação chave](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) no Diário de Atividades Azure Stream Analytics, certifique-se de que o seu trabalho não é afetado por violações de restrições únicas que são suscetíveis de ocorrer durante casos de recuperação. Isto pode ser evitado definindo a opção [IGNORE \_ DUP \_ KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) nos seus índices.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory e In-Memory Tabelas

- **Tabela na memória como tabela temporária** – As [tabelas in-memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) permitem cargas de dados de alta velocidade, mas os dados precisam de se encaixar na memória. Os indicadores de referência mostram que o carregamento a granel de uma tabela em memória para uma tabela baseada em disco é cerca de 10 vezes mais rápido do que a inserção direta a granel usando um único escritor na tabela baseada em disco com uma coluna de identidade e um índice agrupado. Para alavancar este desempenho de inserção a granel, crie uma [cópia utilizando a Azure Data Factory](../data-factory/connector-azure-sql-database.md) que copia dados da tabela de memórias para a tabela baseada em disco.

## <a name="avoiding-performance-pitfalls"></a>Evitar armadilhas de desempenho
Os dados de inserção a granel são muito mais rápidos do que carregar dados com inserções únicas porque a sobrecarga repetida de transferência dos dados, a análise da declaração de inserção, a execução da declaração e a emissão de um registo de transação é evitada. Em vez disso, um caminho mais eficiente é usado no motor de armazenamento para transmitir os dados. O custo de configuração deste caminho é, no entanto, muito maior do que uma única declaração de inserção numa tabela baseada em disco. O ponto de break-even é tipicamente cerca de 100 linhas, além do qual o carregamento a granel é quase sempre mais eficiente. 

Se a taxa de eventos de entrada for baixa, pode facilmente criar tamanhos de lote inferiores a 100 linhas, o que torna a entrada a granel ineficiente e utiliza demasiado espaço em disco. Para contornar esta limitação, pode fazer uma destas ações:
* Crie um [gatilho](/sql/t-sql/statements/create-trigger-transact-sql) em vez de utilizar uma inserção simples para cada linha.
* Utilize uma tabela temporária In-Memory, conforme descrito na secção anterior.

Outro tal cenário ocorre ao escrever num índice de loja de colunas não agrupado (NCCI), onde inserções a granel mais pequenas podem criar demasiados segmentos, que podem colidir com o índice. Neste caso, a recomendação é utilizar um índice de colunas agrupadas.

## <a name="summary"></a>Resumo

Em resumo, com a função de saída partitioned no Azure Stream Analytics para a saída SQL, a paralelização alinhada do seu trabalho com uma mesa dividida em SQL Azure deve dar-lhe melhorias significativas de produção. Alavancar a Azure Data Factory para orquestrar o movimento de dados a partir de uma tabela de In-Memory em tabelas baseadas em disco pode dar ordem de ganhos de produção de magnitude. Se possível, melhorar a densidade da mensagem também pode ser um fator importante para melhorar a produção global.
