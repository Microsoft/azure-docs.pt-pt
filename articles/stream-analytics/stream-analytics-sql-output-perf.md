---
title: Saída de Azure Stream Analytics para o banco de dados SQL do Azure
description: Saiba mais sobre a saída de dados para SQL Azure de Azure Stream Analytics e obter taxas de taxa de transferência de gravação maiores.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443609"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Saída de Azure Stream Analytics para o banco de dados SQL do Azure

Este artigo aborda as dicas para obter um melhor desempenho de taxa de transferência de gravação quando você estiver carregando dados no banco de SQL Azure usando Azure Stream Analytics.

A saída do SQL no Azure Stream Analytics dá suporte à gravação em paralelo como uma opção. Essa opção permite topologias de trabalho [totalmente paralelas](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) , em que várias partições de saída são gravadas na tabela de destino em paralelo. Habilitar essa opção no Azure Stream Analytics no entanto pode não ser suficiente para obter taxas de transferência mais altas, pois depende significativamente da configuração do banco de dados do SQL Azure e do esquema de tabela. A escolha de índices, a chave de clustering, o fator de preenchimento do índice e a compactação têm um impacto no tempo para carregar tabelas. Para obter mais informações sobre como otimizar seu banco de dados SQL Azure para melhorar o desempenho de consulta e carga com base em parâmetros de comparação internos, consulte [diretrizes de desempenho do banco de dados SQL](../sql-database/sql-database-performance-guidance.md). A ordenação de gravações não é garantida ao gravar em paralelo no banco de dados SQL Azure.

Aqui estão algumas configurações em cada serviço que podem ajudar a melhorar a produtividade geral de sua solução.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Herdar particionamento** – essa opção de configuração de saída do SQL permite herdar o esquema de particionamento da entrada ou etapa anterior da consulta. Com isso habilitado, gravando em uma tabela baseada em disco e tendo uma topologia [totalmente paralela](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) para seu trabalho, espere ver melhores taxas de transferência. Esse particionamento já ocorre automaticamente para muitas outras [saídas](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). O bloqueio de tabela (TABLOCK) também é desabilitado para inserções em massa feitas com essa opção.

> [!NOTE] 
> Quando há mais de 8 partições de entrada, a herança do esquema de particionamento de entrada pode não ser uma opção apropriada. Esse limite superior foi observado em uma tabela com uma única coluna de identidade e um índice clusterizado. Nesse caso, considere o uso [de 8 em sua consulta para especificar](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) explicitamente o número de gravadores de saída. Com base no esquema e na escolha de índices, suas observações podem variar.

- **Tamanho do lote** -a configuração de saída do SQL permite que você especifique o tamanho máximo do lote em uma saída de Azure Stream Analytics SQL com base na natureza da sua tabela/carga de trabalho de destino. Tamanho do lote é o número máximo de registros enviados com cada transação de inserção em massa. Em índices columnstore clusterizados, os tamanhos de lote em volta de [100.000](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permitem mais paralelização, registro em log mínimo e otimizações de bloqueio. Em tabelas baseadas em disco, 10K (padrão) ou inferior podem ser ideais para sua solução, já que tamanhos de lote mais altos podem disparar o escalonamento de bloqueios durante inserções em massa.

- **Ajuste de mensagem de entrada** – se você tiver otimizado usando o particionamento de herança e o tamanho do lote, o aumento do número de eventos de entrada por mensagem por partição ajuda a aumentar sua taxa de transferência de gravação. O ajuste de mensagens de entrada permite que tamanhos de lote em Azure Stream Analytics sejam até o tamanho do lote especificado, melhorando assim a taxa de transferência. Isso pode ser obtido usando [compactação](stream-analytics-define-inputs.md) ou aumentando os tamanhos de mensagem de entrada no EventHub ou no BLOB.

## <a name="sql-azure"></a>SQL Azure

- **Tabela e índices particionados** – usar uma tabela do SQL [particionada](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) e índices particionados na tabela com a mesma coluna que sua chave de partição (por exemplo, PartitionID) pode reduzir significativamente as contenções entre partições durante as gravações. Para uma tabela particionada, você precisará criar uma [função de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) e um [esquema de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) no grupo de arquivos primário. Isso também aumentará a disponibilidade dos dados existentes enquanto novos dados estiverem sendo carregados. O limite de e/s de log pode ser atingido com base no número de partições, o que pode ser aumentado atualizando a SKU.

- **Evite violações de chave exclusivas** – se você receber [várias mensagens de aviso de violação de chave](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) no log de atividades Azure Stream Analytics, certifique-se de que seu trabalho não seja afetado por violações de restrição exclusivas que provavelmente ocorrerão durante os casos de recuperação. Isso pode ser evitado definindo a opção [ignorar\_DUP\_chave](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) em seus índices.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory e tabelas na memória

- **Tabela na memória como tabela temporária** – as [tabelas na memória](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) permitem cargas de dados de alta velocidade, mas os dados precisam se ajustar à memória. Os parâmetros de comparação mostram o carregamento em massa de uma tabela na memória para uma tabela baseada em disco é de cerca de 10 vezes mais rápido do que a inserção em massa diretamente usando um único gravador na tabela baseada em disco com uma coluna de identidade e um índice clusterizado. Para aproveitar esse desempenho de inserção em massa, configure um [trabalho de cópia usando Azure data Factory](../data-factory/connector-azure-sql-database.md) que copia dados da tabela na memória para a tabela baseada em disco.

## <a name="avoiding-performance-pitfalls"></a>Evitando armadilhas de desempenho
A inserção em massa de dados é muito mais rápida do que carregar dados com inserções únicas, pois a sobrecarga repetida de transferir os dados, analisar a instrução INSERT, executar a instrução e emitir um registro de transação é evitada. Em vez disso, um caminho mais eficiente é usado no mecanismo de armazenamento para transmitir os dados. No entanto, o custo de configuração desse caminho é muito maior do que uma única instrução INSERT em uma tabela baseada em disco. O ponto de interrupção é geralmente em cerca de 100 linhas, além da qual o carregamento em massa é quase sempre mais eficiente. 

Se a taxa de eventos de entrada for baixa, ela poderá criar facilmente tamanhos de lote inferiores a 100 linhas, o que tornará a inserção em massa ineficiente e usará muito espaço em disco. Para contornar essa limitação, você pode executar uma destas ações:
* Crie um [gatilho](/sql/t-sql/statements/create-trigger-transact-sql) instead of para usar Insert simples para cada linha.
* Use uma tabela temporária na memória, conforme descrito na seção anterior.

Outro cenário desse tipo ocorre ao gravar em um índice columnstore não clusterizado (NCCI), em que inserções em massa menores podem criar muitos segmentos, que podem falhar o índice. Nesse caso, a recomendação é usar um índice Columnstore clusterizado.

## <a name="summary"></a>Resumo

Em resumo, com o recurso de saída particionado em Azure Stream Analytics para saída SQL, a paralelização alinhada de seu trabalho com uma tabela particionada no SQL Azure deve fornecer melhorias significativas na taxa de transferência. Aproveitar Azure Data Factory para orquestrar a movimentação de dados de uma tabela na memória para tabelas baseadas em disco pode fornecer ganhos de taxa de transferência de magnitude. Se possível, melhorar a densidade da mensagem também pode ser um fator importante na melhoria da produtividade geral.
