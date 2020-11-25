---
title: Melhores práticas de carregamento de dados
description: Recomendações e otimizações de desempenho para carregar dados em um pool de SQL dedicado Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7e706f12a251cd38c3525a48553743606ed199b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024032"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Melhores práticas para carregar dados em um pool de SQL dedicado Azure Synapse Analytics

Neste artigo, encontrará recomendações e otimizações de desempenho para carregar dados.

## <a name="prepare-data-in-azure-storage"></a>Preparar dados no Azure Storage

Para minimizar a latência, coloque a sua camada de armazenamento e a sua piscina SQL dedicada.

Ao exportar dados para um Formato de Ficheiro ORC, poderá obter erros de memória esgotada Java quando existem colunas de texto grandes. Para contornar esta limitação, exporte apenas um subconjunto de colunas.

A PolyBase não consegue carregar linhas que tenham mais de 1.000.000 bytes de dados. Quando colocar dados nos ficheiros de texto no armazenamento de Blobs do Azure ou no Azure Data Lake Store, aqueles têm de ter menos de 1 000 000 bytes. Esta limitação de bytes acontece independentemente do esquema de tabela.

Todos os formatos de ficheiro têm características de desempenho diferentes. Para a carga mais rápida, utilize ficheiros de texto delimitados e comprimidos. A diferença entre o desempenho de UTF-8 e UTF-16 é mínima.

Divida grandes ficheiros comprimidos em ficheiros mais pequenos comprimidos.

## <a name="run-loads-with-enough-compute"></a>Executar cargas com cálculo suficiente

Para a velocidade de carregamento mais rápida, execute apenas uma tarefa de carregamento de cada vez. Se não for viável, execute um número mínimo de cargas em simultâneo. Se espera um grande trabalho de carregamento, considere aumentar a sua piscina SQL dedicada antes da carga.

Para executar cargas com recursos de computação adequados, crie utilizadores de carregamento designados para a execução de cargas. Atribua cada utilizador de carregamento a uma classe de recursos específica ou grupo de carga de trabalho. Para executar uma carga, inscreva-se como um dos utilizadores de carregamento e, em seguida, carregue a carga. A carga é executada com a classe de recursos do utilizador.  Este método é mais simples do que tentar alterar a classe de recursos de um utilizador para que se ajuste à necessidade da classe de recursos atual.

### <a name="create-a-loading-user"></a>Criar um utilizador de carregamento

Este exemplo cria um utilizador de carregamento para a classe de recursos staticrc20. O primeiro passo é **ligar ao master** e criar um início de sessão.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Ligar ao armazém de dados e criar um utilizador. O seguinte código partem do princípio de que está ligado à base de dados chamada mySampleDataWarehouse. Mostra como criar um utilizador chamado LoaderRC20, conceder a permissão de controlo de utilizador numa base de dados. Em seguida, adiciona o utilizador como um membro da função de base de dados staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Para executar uma carga com recursos para as classes de recursos staticRC20, inscreva-se como LoaderRC20 e execute a carga.

Execute as cargas em classes de recursos estáticas em vez de dinâmicas. A utilização das classes de recursos estáticos garante os mesmos recursos, independentemente das suas unidades de armazém de [dados.](resource-consumption-models.md) Se utilizar uma classe de recursos dinâmica, os recursos variam de acordo com o nível de serviço. Nas classes dinâmicas, um nível de serviço mais baixo significa que terá de, provavelmente, utilizar uma classe de recursos maior para o seu utilizador de carregamento.

## <a name="allow-multiple-users-to-load"></a>Permitir que vários utilizadores carreguem

Muitas vezes, é necessário ter vários utilizadores a realizar carregamentos para um armazém de dados. O carregamento com a [tabela CREATE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) requer permissões de CONTROLO da base de dados.  A permissão de CONTROL permite controlar o acesso a todos os esquemas. Poderá não querer que todos os utilizadores de carregamento tenham acesso de controlo em todos os esquemas. Para limitar as permissões, utilize a instrução DENY CONTROL.

Por exemplo, considere os esquemas de bases de dados schema_A, para departamento A, e schema_B, para departamento B. Permita que os utilizadores user_A e user_B sejam utilizadores do carregamento PolyBase nos departamentos A e B, respetivamente. Foram concedidas a ambos permissões de base de dados CONTROL. Agora, os criadores dos esquemas A e B bloqueiam os esquemas com DENY.

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B estão agora bloqueados do esquema do outro departamento.

## <a name="load-to-a-staging-table"></a>Carregar para uma tabela de teste

Para alcançar a velocidade de carregamento mais rápida para mover dados para uma tabela de armazém de dados, carregue dados para uma tabela de testes.  Definir a tabela de testes como uma área dinâmica para dados e utilizar o round-robin como opção de distribuição.

Considere que o carregamento é, normalmente, um processo de dois passos no qual pode carregar primeiro para uma tabela de teste e, em seguida, inserir os dados numa tabela de armazém de dados de produção. Se a tabela de produção utiliza uma distribuição hash, o tempo total para carregar e inserir poderá ser mais rápido, se definir a tabela de testes com a distribuição hash. O carregamento para a tabela de teste demora mais tempo, mas o segundo passo de inserção das linhas na tabela de produção não implica o movimento de dados entre as distribuições.

## <a name="load-to-a-columnstore-index"></a>Carregar para um índice de loja de colunas

Os índices columnstore exigem grandes quantidades de memória para comprimir os dados em grupos de linhas de elevada qualidade. Para a melhor eficiência em termos de compressão e indexação, o índice columnstore tem de comprimir um máximo de 1 048 576 linhas em cada rowgroup. Quando existe pressão de memória, o índice columnstore poderá não conseguir alcançar as velocidades de compressão máxima. Isto afeta o desempenho da consulta. Para obter uma descrição aprofundada, veja [Columnstore memory optimizations](data-load-columnstore-compression.md) (Otimizações de memória de columnstore).

- Para garantir que o utilizador de carregamento tem memória suficiente para alcançar as velocidades de compressão máximas, utilize utilizadores de carregamento que sejam membros de uma classe de recursos média ou grande.
- Carregue linhas suficientes para preencher completamente rowgroups novos. Durante uma carga a granel, cada 1.048.576 linhas são comprimidos diretamente na loja de colunas como um grupo de fila completa. Carregamentos com menos de 102,400 linhas enviam as linhas para o deltastore onde as linhas são guardadas num índice de árvore b. Se carregar muito poucas linhas, podem ir todas para o deltastore e não ser comprimidas de imediato no formato columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumente o tamanho do lote ao utilizar a API da SQLBulkCopy ou o BCP

Como mencionado anteriormente, o carregamento com PolyBase fornecerá a maior produção com piscina Sinaapse SQL. Se não puder utilizar o PolyBase para carregar e tiver de utilizar a API SQLBulkCopy (ou BCP), deve considerar o aumento do tamanho do lote para uma melhor produção - uma boa regra do polegar é um tamanho de lote entre 100k e 1M linhas.

## <a name="manage-loading-failures"></a>Gerir falhas de carregamento

Uma carga que utilize uma tabela externa pode falhar com o erro *"Query aborted-- the maximum reject threshold was reached while reading from an external source"* (“Consulta abortada. O limiar de rejeição máximo foi atingido ao ler a partir de uma origem externa”). Esta mensagem indica que os dados externos contém registos desatualizados. Os registos de dados são considerados desatualizados se os tipos de dados e número de colunas não corresponderem às definições de coluna da tabela externa ou se os dados não estiverem em conformidade com o formato de ficheiro externo especificado.

Para corrigir os registos desatualizados, confirme que as definições de tabela externa e de formato de ficheiro externo estão corretas e que os dados externos estão em conformidade com estas definições. Caso um subconjunto de registos de dados externos esteja desatualizado, pode optar por rejeitar esses registos nas suas consultas mediante a utilização das opções de rejeição em CREATE EXTERNAL TABLE (CRIAR TABELA EXTERNA).

## <a name="insert-data-into-a-production-table"></a>Inserir dados numa tabela de produção

Um carregamento único para uma pequena tabela com uma instrução [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ou mesmo um recarregamento periódico de uma consulta pode ajustar-se às suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, as inserções singleton não são tão eficientes como a realização de uma carga a granel.

Se tiver milhares ou mais de inserções individuais durante o dia, junte-as para poder carregá-las em massa.  Desenvolva os seus processos de modo a que anexem as inserções individuais a um ficheiro e crie outro processo que o carregue periodicamente.

## <a name="create-statistics-after-the-load"></a>Criar estatísticas após o carregamento

Para melhorar o desempenho da consulta, é importante criar estatísticas em todas as colunas de todas as tabelas após a primeira carga, ou ocorrerem grandes alterações nos dados. Criar estatísticas pode ser feito manualmente ou pode [ativar estatísticas de criação automática](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para uma explicação detalhada das estatísticas, consulte [Estatísticas](develop-tables-statistics.md). O exemplo a seguir mostra como criar manualmente estatísticas em cinco colunas da tabela Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Alternar chaves de armazenamento

É boa prática de segurança alterar a chave de acesso ao seu armazenamento de blobs regularmente. Tem duas chaves de armazenamento para a conta de armazenamento de blobs, que lhe permite efetuar a transição das chaves.

Para alternar as chaves da conta de Armazenamento do Azure:

Para cada conta de armazenamento cuja chave mudou, emita [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Exemplo:

A chave original é criada

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Efetue a rotação de chaves da chave 1 para a chave 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Não é preciso fazer outras alterações às origens de dados externas subjacentes.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a PolyBase e conceber um processo de extrato, carga e transformação (ELT), consulte [Design ELT para Azure Synapse Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Para um tutorial de carregamento, [utilize a PolyBase para carregar os dados do armazenamento da bolha Azure para a Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Para monitorizar o carregamento de dados, veja [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Monitorizar a sua carga de trabalho com redes de perímetro).
