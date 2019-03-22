---
title: Saída do Azure Stream Analytics para a base de dados do Azure SQL
description: Saiba mais sobre a saída de dados do Azure Stream Analytics para o SQL Azure e obter taxas de débito de escrita mais elevadas.
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: d259fd5fc8c60837c6b6110eb751360227d70836
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338433"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Saída do Azure Stream Analytics para a base de dados do Azure SQL

Este artigo aborda dicas para obter um melhor desempenho de taxa de transferência de escrita quando está carregando dados na base de dados do SQL Azure com o Azure Stream Analytics.

Saída SQL no Azure Stream Analytics oferece suporte a escrever em paralelo, como uma opção. Esta opção permite [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) topologias, em que estiver a escrever várias partições de saída para a tabela de destino em paralelo da tarefa. Ativar esta opção no Azure Stream Analytics no entanto pode não ser suficiente para obter as taxas de transferência maior, pois dependem significativamente sua configuração de base de dados do SQL Azure e o esquema da tabela. A escolha de índices, clustering de chave, o fator de preenchimento do índice e compressão de ter um impacto sobre o tempo de carregamento de tabelas. Para obter mais informações sobre como otimizar seu banco de dados do SQL Azure para melhorar a consulta e desempenho com base em parâmetros de comparação internos de carga, veja [orientação de desempenho de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance). Não é garantido a ordenação de escrita ao escrever em paralelo para a base de dados do SQL Azure.

Aqui estão algumas configurações dentro de cada serviço que pode ajudar a melhorar o débito global da sua solução.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Criação de partições de herdar** – SQL esta saída configuração opção permite a herdar de esquema de partições do seu passo de consulta anterior ou a entrada. Com esta opção ativada, a escrever para uma tabela baseada em disco e ter uma [totalmente paralela](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) topologia para a sua tarefa, esperar ver as taxas de transferência melhor. Essa divisão já automaticamente acontece para muitas outras [produz](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Bloqueio de tabela (TABLOCK) também está desativada para inserções em massa feitas com esta opção.

> [!NOTE] 
> Quando há mais de 8 partições de entrada, herdando a entrada do esquema de particionamento pode não ser uma opção adequada. Este limite superior foi observada numa tabela com uma coluna de identidade única e um índice em cluster. Com base no seu esquema e a escolha de índices, as observações podem variar.

- **Tamanho do lote** -configuração de saída do SQL permite-lhe especificar o tamanho de lote máximo numa saída de SQL do Azure Stream Analytics com base na natureza do seu destino tabela/carga de trabalho. Tamanho do lote é o número máximo de registos que são enviados com cada em massa inserir a transação. Em índices columnstore em cluster, tamanhos em torno do batch [100 mil](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permitir mais paralelização, um mínimo de registo e otimizações de bloqueio. Em tabelas baseada em disco, 10 mil (predefinição) ou inferior pode ser ideal para a sua solução, como tamanhos de batch superior poderão acionar o escalonamento de bloqueio durante inserções em massa.

- **Otimização de mensagem de entrada** – se já otimizados utilizando a herdar de tamanho de lote e de criação de partições, aumento do número de eventos de entrada por mensagem por partição ajuda ainda mais enviar até o débito de escrita. Otimização de mensagem de entrada permite que os tamanhos de batch dentro do Azure Stream Analytics ser até ao tamanho de lote especificado, que melhora o débito. Isso pode ser obtido usando [compressão](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs) ou aumentar os tamanhos de mensagem de entrada no Blob ou EventHub.

## <a name="sql-azure"></a>SQL Azure

- **Particionadas e índices de tabela** – utilizando um [particionada](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabela SQL e índices particionados na tabela com a mesma coluna como sua chave de partição (por exemplo, PartitionId) podem reduzir significativamente as contenções entre partições durante a escrita. Para uma tabela particionada, terá de criar uma [função de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) e uma [esquema de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) no grupo de ficheiros primário. Isto também irá aumentar a disponibilidade dos dados existentes enquanto está a ser carregados novos dados. Pode ser atingido o limite de e/s de registo com base no número de partições, que pode ser aumentada ao atualizar o SKU.

- **Evitar a violação de chave exclusivas** – se obtiver [várias mensagens de aviso de violação de chave](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) no registo de atividade do Azure Stream Analytics, certifique-se a tarefa não é afetada pelo violações de restrição exclusiva que são provável que aconteça durante a casos de recuperação. Isso pode ser evitado, definindo a [ignorar\_duplicado\_chave](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) opção nos índices.

## <a name="azure-data-factory-and-in-memory-tables"></a>Fábrica de dados do Azure e de tabelas em memória

- **Tabela em memória como uma tabela temporária** – [tabelas em memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) permitem cargas de dados de alta velocidade, mas tem de dados caber na memória. Parâmetros de comparação show em massa carregando a partir de uma tabela em memória a uma tabela baseada em disco é cerca de 10 vezes mais rápido do que diretamente em massa inserir usando um único gravador para a tabela baseada em disco com uma coluna de identidade e um índice em cluster. Para aproveitar este desempenho de inserção em massa, configure uma [tarefa de cópia com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database) que copia dados da tabela dentro da memória para a tabela baseada em disco.

## <a name="avoiding-performance-pitfalls"></a>Evitando armadilhas de desempenho
Inserindo dados em massa é muito mais rápida do que carregar dados com inserções individuais porque o repetidas sobrecarga de transferência de dados, análise da instrução insert, executar a instrução e emitir um registo de transação é evitada. Em vez disso, um caminho mais eficiente é utilizado para o mecanismo de armazenamento para transmitir os dados. O custo de configuração deste caminho é no entanto muito maior do que uma instrução insert único numa tabela baseada em disco. O ponto de rentabilidade é, normalmente, cerca de 100 linhas, além do que em massa ao carregar quase sempre é mais eficiente. 

Se a taxa de receção de eventos for baixa, ele pode criar facilmente tamanhos de lote menor do que 100 linhas, o que faz em massa inserir ineficiente e usa muito espaço em disco. Para contornar esta limitação, pode fazer uma destas ações:
* Criar um INSTEAD OF [acionador](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-trigger-transact-sql) para utilizar a inserção simples para cada linha.
* Utilize uma tabela temporária de dentro da memória, conforme descrito na secção anterior.

Outro cenário como esse ocorre ao escrever para um índice columnstore não em cluster (NCCI), onde mais pequenas inserções de em massa podem criar muitos segmentos, que podem causar falhas o índice. Neste caso, a recomendação é usar um índice Columnstore em cluster em vez disso.

## <a name="summary"></a>Resumo

Em resumo, com a funcionalidade de saída particionadas no Azure Stream Analytics para a saída SQL, alinhada paralelização de seu trabalho com uma tabela particionada no SQL Azure deve dar a melhorias significativas de débito. Tirar partido do Azure Data Factory para orquestrar o movimento de dados a partir de uma tabela dentro da memória em tabelas baseada em disco pode permitir que os ganhos de débito de ordem de magnitude. Se for viável, melhorando a densidade de mensagem também pode ser um grande fator de melhorar a produtividade geral.
