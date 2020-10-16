---
title: Melhores práticas de desenvolvimento para Sinapse SQL
description: Recomendações e boas práticas que deve conhecer à medida que se desenvolve para o Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: fe00d7f107911e2245041419c20f86e2e32a0480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289264"
---
# <a name="development-best-practices-for-synapse-sql"></a>Melhores práticas de desenvolvimento para Sinapse SQL
Este artigo descreve orientação e boas práticas à medida que desenvolve a sua solução de armazém de dados. 

## <a name="sql-pool-development-best-practices"></a>As melhores práticas de desenvolvimento de piscinas SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Manter as estatísticas

Certifique-se de que atualiza as suas estatísticas diariamente ou depois de cada carga.  Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas. Se achar que está a demorar muito tempo a manter todas as suas estatísticas, seja mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de atualização frequente.  

Por exemplo, é possível que pretenda atualizar colunas de datas, onde novos valores podem ser adicionados diariamente. 

> [!NOTE]
> Você ganhará o maior benefício com estatísticas sobre colunas envolvidas em junções, colunas usadas na cláusula WHERE, e colunas encontradas no GROUP BY.

Consulte também [As estatísticas dos quadros,](develop-tables-statistics.md) [CRIAR ESTATÍSTICAS,](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [ESTATÍSTICAS DE ATUALIZAÇÃO](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin. Esta capacidade facilita a criação de tabelas por parte dos utilizadores sem terem de decidir como devem ser distribuídas as suas tabelas.  As mesas robin redondas podem funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, selecionar uma coluna de distribuição terá um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de encomendas distribuída por order_id, e uma tabela de transações, também distribuída por order_id, quando se junta à sua tabela de encomendas à sua tabela de transações em order_id, esta consulta torna-se uma consulta de passagem. 

Isto significa que eliminamos as operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.

> [!TIP]
> Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  

Consulte os seguintes links para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula WITH da sua declaração CREATE TABLES.

Consulte também [a visão geral da tabela,](develop-tables-overview.md) [distribuição de tabelas,](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [selecionando a distribuição da tabela,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [CRIE TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e CRIE TABELA [COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora os dados de partição possam ser eficazes para manter os seus dados através da comutação de partição ou otimização de digitalizações com eliminação de divisórias, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  

> [!NOTE]
> Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  

Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas. A sql pool partilha os seus dados para si em 60 bases de dados. 

Então, se criar uma mesa com 100 divisórias, o resultado será 6000 divisórias.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  

Uma opção a considerar é usar uma granularidade inferior ao que pode ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [a divisão da tabela.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERT que espera levar 1 hora, pode partir o INSERT em quatro partes, encurtando assim cada percurso para 15 minutos.

> [!TIP]
> Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  

Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas não participantes, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação muito mais segura, uma vez que tem registo de transações mínimos e pode ser cancelada rapidamente, se necessário.

Consulte também [as transações de compreensão](develop-transactions.md), [Otimização de transações,](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [divisão de tabelas,](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [TABELA TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [TABELA ALTER,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [Criar tabela como selecionado (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, a utilização do menor tipo de dados que irá suportar os seus dados melhorará o desempenho da consulta. Esta ação é especialmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário em vez de usar NVARCHAR.

Consulte também [a visão geral da tabela,](develop-tables-overview.md) [os tipos de dados de tabela](develop-tables-data-types.md)e a [CRIAÇÃO DE TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de loja de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados na piscina SQL.  Por predefinição, as tabelas na piscina SQL são criadas como Clustered ColumnStore.  

Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  

A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [causas da má qualidade do índice de colunas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e artigo de [índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs de utilizadores que estão na classe de recursos médios ou grandes para carregar dados. A utilização de unidades de [armazém de dados](resource-consumption-models.md) mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimidos até que haja mais de 1 milhão de linhas por tabela, e cada mesa de bilhar SQL é dividida em 60 tabelas, as mesas de loja de colunas não beneficiarão uma consulta a menos que a mesa tenha mais de 60 milhões de linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de loja de colunas pode não ser a solução ideal.  

Além disso, se dividir os seus dados, então você vai considerar que cada partição precisa ter 1 milhão de linhas para beneficiar de um índice de colunas agrupados.  Se uma mesa tiver 100 divisórias, terá de ter pelo menos 6 mil milhões de linhas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a sua mesa não tiver 6 mil milhões de linhas, reduza o número de divisórias ou considere usar uma mesa de amontoados.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela de amontoados com índices secundários em vez de uma tabela de lojas de colunas.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [guia de índices de colunas,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [índices de loja de colunas de reconstrução](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>ASL a pedido de desenvolvimento das melhores práticas

### <a name="general-considerations"></a>Considerações gerais

O SQL on demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades de armazenamento ou ingestão locais, o que significa que todos os ficheiros que os alvos de consulta são externos ao SQL on-demand. Então, tudo relacionado com a leitura de ficheiros de armazenamento pode ter um impacto no desempenho da consulta.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Conta de Armazenamento ColocaTe Azure e SQL a pedido

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido do SQL. As contas de armazenamento e os pontos finais a provisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para um melhor desempenho, se aceder a outras contas de armazenamento com SQL a pedido, certifique-se de que estão na mesma região. Caso contrário, haverá um aumento da latência para a transferência da rede de dados da região remota para a região do ponto final.

### <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento Azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. Quando o IOPS combinado ou a produção gerada por aplicações, serviços e carga de trabalho a pedido da SQL excedem os limites da conta de armazenamento, ocorre estrangulamento de armazenamento. Quando ocorre estrangulamento de armazenamento, há um efeito negativo substancial no desempenho da consulta.

Uma vez detetado o estrangulamento, o SQL on-demand tem incorporado no manuseamento deste cenário. A SQL on demand fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido. 

No entanto, para uma execução ótima de consultas, é aconselhável que não exteste a conta de armazenamento com outras cargas de trabalho durante a execução de consultas.

### <a name="prepare-files-for-querying"></a>Preparar ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV para Parquet – Parquet é formato colunar. Uma vez que é comprimido, tem tamanhos de ficheiros menores do que ficheiros CSV com os mesmos dados, e SQL a pedido precisará de menos tempo e pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiará de o dividir para vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo dos 10GB.
- É preferível ter ficheiros de tamanho igual para um único caminho OPENROWSET ou uma LOCALIZAÇÃO de tabela externa.
- Partition seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros - verifique [usar funções de nome de ficheiro e filepa para direcionar divisórias específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Use funções de fileinfo e filepath para direcionar divisórias específicas

Os dados são muitas vezes organizados em divisórias. Pode instruir o SQL a pedido para consultar determinadas pastas e ficheiros. Ao fazê-lo, reduzirá o número de ficheiros e a quantidade de dados que a consulta necessita para ler e processar. 

Consequentemente, conseguirá um melhor desempenho. Para obter mais informações, verifique funções de [nome de ficheiros](query-data-storage.md#filename-function) e [ficheiros](query-data-storage.md#filepath-function) e exemplos sobre como [consultar ficheiros específicos](query-specific-files.md).

Se os seus dados no armazenamento não forem divididos, considere a divisão para que possa utilizar estas funções para otimizar as consultas direcionadas para esses ficheiros.

Ao [consultar as tabelas externas do Azure Synapse](develop-storage-files-spark-tables.md) a partir do SQL on demand, a consulta destina-se automaticamente apenas aos ficheiros necessários.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

[O CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis no SQL on demand. CETAS é uma operação paralela que cria metadados de mesa externos e exporta o resultado da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar frequentemente parte das consultas usadas, como tabelas de referência juntas, a um novo conjunto de ficheiros. Mais tarde, pode juntar-se a esta única tabela externa em vez de repetir junções comuns em múltiplas consultas. 

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta atingir esta tabela externa e ganhará um desempenho melhorado.

### <a name="next-steps"></a>Passos seguintes

Se precisar de informações não fornecidas neste artigo, utilize a função **De procurar por doc** no lado esquerdo desta página para pesquisar todos os documentos da piscina SQL.  O [Microsoft Q&Uma página de perguntas para piscina SQL](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) é um local para colocar questões a outros utilizadores e ao Grupo de Produtos de piscina SQL.  

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se preferir fazer as suas perguntas sobre Stack Overflow, também temos um [Azure SQL pool Stack Overflow Forum.](https://stackoverflow.com/questions/tagged/azure-sqldw)
 
