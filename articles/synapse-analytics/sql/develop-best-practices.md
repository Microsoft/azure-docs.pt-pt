---
title: As melhores práticas de desenvolvimento para synapse SQL
description: Recomendações e boas práticas que deve conhecer à medida que se desenvolve para synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 858dfa8282a06feab877bcea65de47b8455133dc
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697303"
---
# <a name="development-best-practices-for-synapse-sql"></a>As melhores práticas de desenvolvimento para synapse SQL
Este artigo descreve orientações e boas práticas à medida que desenvolve a sua solução de armazém de dados. 

## <a name="sql-pool-development-best-practices"></a>Boas práticas de desenvolvimento de piscinaS SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Manter as estatísticas

Certifique-se de atualizar as suas estatísticas diariamente ou depois de cada carga.  Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas. Se descobrir que está a demorar muito tempo a manter todas as suas estatísticas, seja mais seletivo sobre quais as colunas que têm estatísticas ou quais as colunas que precisam de atualização frequente.  

Por exemplo, é possível atualizar as colunas de datas, onde novos valores podem ser adicionados diariamente. 

> [!NOTE]
> Você ganhará o maior benefício por ter estatísticas sobre colunas envolvidas em juntas, colunas utilizadas na cláusula WHERE, e colunas encontradas no GRUPO BY.

Ver também Gerir estatísticas de [tabelas,](develop-tables-statistics.md) [CRIAR ESTATÍSTICAS,](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ESTATÍSTICAS DE [ATUALIZAÇÃO](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.  Isto facilita que os utilizadores comecem a criar tabelas sem terem de decidir como devem ser distribuídas as suas tabelas.  As mesas redondas robin podem funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, selecionar uma coluna de distribuição terá um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de encomendas, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando se junta à tabela de encomendas na tabela de transações sobre order_id, esta consulta torna-se uma consulta pass-through. 

Isto significa que eliminamos as operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.

> [!TIP]
> Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  

Consulte os seguintes links para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula WITH da sua declaração CREATE TABLES.

Consulte também [a visão geral da tabela,](develop-tables-overview.md)distribuição de [tabelas,](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)distribuição [de tabelas,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [MESA DE CRIAÇÃO,](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [CRIAR TABELA COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora a partilha de dados possa ser eficaz para manter os seus dados através da troca de divisórias ou otimização de digitalizações com a eliminação da divisória, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  

> [!NOTE]
> Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  

Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas. A partilha de divisórias do sql em 60 bases de dados. 

Então, se criar uma mesa com 100 divisórias, o resultado serão 6000 divisórias.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  

Uma opção a considerar é usar uma granularidade inferior ao que pode ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Ver também [partição de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERTo que espere demorar 1 hora, pode dividir o INSerto em quatro partes, encurtando assim cada corrida para 15 minutos.

> [!TIP]
> Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  

Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas sem partição considere utilizar um CTAS para escrever os dados que pretende manter numa tabela, em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação mais segura uma vez que tem um registo de transações mínimo e pode ser cancelada rapidamente se for necessário.

Ver também [Compreender as transações,](develop-transactions.md) [otimizar transações,](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [partição em tabela,](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [Tabela TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ALTER TABLE,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [Criar tabela como selecionado (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Quando definir a DDL, utilize o tipo de dados mais pequeno que suporte os dados, de forma a melhorar o desempenho das consultas.  Isto é especialmente importante para colunas CHAR e VARCHAR.  

Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [a visão geral da tabela,](develop-tables-overview.md)os tipos de dados da [tabela](develop-tables-data-types.md)e a [TABELA CREATE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados em piscina SQL.  Por padrão, as tabelas na piscina SQL são criadas como ColumnStore Clustered.  

Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  

A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [causas da má qualidade do índice de colunas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e do artigo [dos índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs dos utilizadores que estão na classe média ou grande de recursos para carregar dados. A utilização de [unidades](resource-consumption-models.md) de armazém de dados mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimido até que existam mais de 1 milhão de linhas por mesa, e cada mesa de bilhar SQL é dividida em 60 tabelas, as tabelas de lojas de colunas não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de columstore pode não ser a solução ideal.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 divisórias, então terá de ter pelo menos 6 mil milhões de filas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a sua mesa não tiver 6 mil milhões de linhas, reduza o número de divisórias ou considere utilizar uma tabela de heap.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela heap com índices secundários em vez de uma tabela de colunas.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Ver também [índices de tabela,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [guia de índices de colunas,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Reconstrução de índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)de colunas .

## <a name="sql-on-demand-development-best-practices"></a>Boas práticas de desenvolvimento a pedido da SQL

### <a name="general-considerations"></a>Considerações gerais

A SQL on-demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão, o que significa que todos os ficheiros que os alvos de consulta são externos à SQL a pedido. Portanto, tudo relacionado com a leitura de ficheiros do armazenamento pode ter um impacto no desempenho da consulta.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Conta de Armazenamento Colocate Azure e SQL a pedido

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido da SQL. As contas de armazenamento e os pontos finais aprovisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para um desempenho ótimo, se aceder a outras contas de armazenamento com sql on-demand, certifique-se de que estão na mesma região. Caso contrário, haverá um aumento da latência para a transferência de rede dos dados da região remota para a região do ponto final.

### <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. Quando o IOPS combinado ou a potência gerada por aplicações, serviços e carga de trabalho a pedido da SQL excederem os limites da conta de armazenamento, o estrangulamento de armazenamento ocorre. Quando ocorre a aceleração do armazenamento, há um efeito negativo substancial no desempenho da consulta.

Uma vez detetada a aceleração, a SQL on-demand tem um manuseamento incorporado deste cenário. A SQL a pedido fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido. 

No entanto, para uma execução de consulta ideal, é aconselhável não sublinhar a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

### <a name="prepare-files-for-querying"></a>Prepare ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV para Parquet – Parquet é formato colunar. Uma vez que é comprimido, tem tamanhos de ficheiros menores do que ficheiros CSV com os mesmos dados, e a SQL a pedido necessitará de menos tempo e pedidos de armazenamento para lê-lo.
- Se uma consulta visa um único ficheiro grande, beneficiará de dividi-lo em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo dos 10GB.
- É preferível ter ficheiros de tamanho igual para um único caminho OPENROWSET ou uma localização de mesa externa.
- Partifique os seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros - verifique funções de nome de [ficheiro e de ficheiros para direcionar divisórias específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utilize funções de fileinfo e filepath para direcionar divisórias específicas

Os dados são frequentemente organizados em divisórias. Pode instruir a SQL a pedido para consultar determinadas pastas e ficheiros. Isto reduzirá o número de ficheiros e a quantidade de dados que a consulta precisa de ler e processar. 

Consequentemente, conseguirá um melhor desempenho. Para mais informações, verifique o nome de [ficheiros](develop-storage-files-overview.md#filename-function) e as funções [de path path](develop-storage-files-overview.md#filepath-function) e exemplos sobre como consultar [ficheiros específicos](query-specific-files.md).

Se os seus dados no armazenamento não forem divididos, considere dividi-los para que possa utilizar estas funções para otimizar consultas direcionadas a esses ficheiros.

Ao [consultar a Apache Spark dividida para tabelas externas Azure Synapse](develop-storage-files-spark-tables.md) a partir da SQL a pedido, a consulta irá automaticamente direcionar apenas os ficheiros necessários.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

O [CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis na SQL a pedido. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta o resultado da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar parte frequentemente utilizada de consultas, como tabelas de referência juntas, para um novo conjunto de ficheiros. Mais tarde, pode juntar-se a esta única tabela externa em vez de repetir juntas comuns em múltiplas consultas. 

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão automaticamente criadas quando a primeira consulta visa esta tabela externa e obterá um desempenho melhorado.

### <a name="next-steps"></a>Passos seguintes

Se precisar de informações não fornecidas neste artigo, utilize a "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos da piscina SQL.  O Fórum de [Piscina SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para colocar questões a outros utilizadores e ao Grupo de Produtos de Piscina SQL.  

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se você prefere fazer suas perguntas sobre Stack Overflow, também temos uma [piscina Azure SQL Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).
 