---
title: Dados que carregam as melhores práticas para piscinas SQL dedicadas
description: Recomendações e otimizações de desempenho para carregamento de dados utilizando piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a96d49a029eb83e24c1fb86954406693aa9c33a3
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093966"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Melhores práticas para carregar dados usando piscinas SQL dedicadas em Azure Synapse Analytics

Neste artigo, você vai aprender recomendações e otimizações de desempenho para carregar dados usando pool SQL dedicado.

## <a name="preparing-data-in-azure-storage"></a>Preparar dados no Armazenamento do Azure

Para minimizar a latência, coloque a sua camada de armazenamento e a sua piscina SQL dedicada.

Ao exportar dados para um Formato de Ficheiro ORC, poderá obter erros de memória esgotada Java quando existem colunas de texto grandes. Para contornar esta limitação, exporte apenas um subconjunto de colunas.

Todos os formatos de ficheiro têm características de desempenho diferentes. Para a carga mais rápida, utilize ficheiros de texto delimitados e comprimidos. A diferença entre o desempenho de UTF-8 e UTF-16 é mínima.

Divida grandes ficheiros comprimidos em ficheiros mais pequenos comprimidos.

## <a name="running-loads-with-enough-compute"></a>Executar cargas com computação suficiente

Para a velocidade de carregamento mais rápida, execute apenas uma tarefa de carregamento de cada vez. Se isso não for viável, executar um número mínimo de cargas simultaneamente. Se espera um grande trabalho de carregamento, considere aumentar a sua piscina SQL dedicada antes da carga.

Para executar cargas com recursos de computação adequados, crie utilizadores de carregamento designados para a execução de cargas. Classifique cada utilizador de carga a um grupo de carga específico. Para executar uma carga, inscreva-se como um dos utilizadores de carregamento e, em seguida, carregue a carga. A carga é de funcionada com o grupo de trabalho do utilizador.  

### <a name="example-of-creating-a-loading-user"></a>Exemplo de como criar um utilizador de carregamento

Este exemplo cria um utilizador de carregamento classificado para um grupo de carga específico. O primeiro passo é **ligar ao master** e criar um início de sessão.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Ligue-se à piscina SQL dedicada e crie um utilizador. O seguinte código pressupõe que está ligado à base de dados chamada mySampleDataWarehouse. Mostra como criar um utilizador chamado carregador e dá ao utilizador permissões para criar tabelas e carregar utilizando a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Em seguida, classifica o utilizador para o grupo de carga de trabalho DataLoads com o máximo de recursos. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
      MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Este é um exemplo extremo de alocar 100% de recursos da piscina SQL a uma única carga. Isto lhe dará uma concordância máxima de 1. Esteja ciente de que este deve ser usado apenas para a carga inicial onde você precisará criar grupos de carga de trabalho adicionais com as suas próprias configurações para balanace recursos através das suas cargas de trabalho. 

Para executar uma carga com recursos para o grupo de carga de carga, inscreva-se como carregador e carregue a carga.

## <a name="allowing-multiple-users-to-load-polybase"></a>Permitindo que vários utilizadores carreguem (PolyBase)

Muitas vezes é necessário que vários utilizadores carreguem dados numa piscina de SQL dedicada. O carregamento com a [tabela CREATE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (PolyBase) requer permissões de CONTROLO da base de dados.  A permissão de CONTROL permite controlar o acesso a todos os esquemas.

Poderá não querer que todos os utilizadores de carregamento tenham acesso de controlo em todos os esquemas. Para limitar as permissões, utilize a instrução DENY CONTROL.

Por exemplo, considere os esquemas de bases de dados schema_A, para departamento A, e schema_B, para departamento B. Permita que os utilizadores user_A e user_B sejam utilizadores do carregamento PolyBase nos departamentos A e B, respetivamente. Foram concedidas a ambos permissões de base de dados CONTROL. Agora, os criadores dos esquemas A e B bloqueiam os esquemas com DENY.

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B estão agora bloqueados do esquema do outro departamento.

## <a name="loading-to-a-staging-table"></a>Carregar para uma tabela de teste

Para obter a velocidade de carregamento mais rápida para mover dados para uma mesa de bilhar SQL dedicada, carregue os dados numa tabela de preparação.  Definir a tabela de testes como uma área dinâmica para dados e utilizar o round-robin como opção de distribuição.

Considere que o carregamento é geralmente um processo em duas etapas em que primeiro carrega para uma mesa de preparação e, em seguida, insira os dados numa mesa de bilhar SQL dedicada à produção. Se a tabela de produção utiliza uma distribuição hash, o tempo total para carregar e inserir poderá ser mais rápido, se definir a tabela de testes com a distribuição hash.

O carregamento para a tabela de teste demora mais tempo, mas o segundo passo de inserção das linhas na tabela de produção não implica o movimento de dados entre as distribuições.

## <a name="loading-to-a-columnstore-index"></a>Carregamento para um índice columnstore

Os índices columnstore exigem grandes quantidades de memória para comprimir os dados em grupos de linhas de elevada qualidade. Para a melhor eficiência em termos de compressão e indexação, o índice columnstore tem de comprimir um máximo de 1 048 576 linhas em cada rowgroup.

Quando existe pressão de memória, o índice columnstore poderá não conseguir alcançar as velocidades de compressão máxima. Este cenário, por sua vez, afeta o desempenho da consulta. Para obter uma descrição aprofundada, veja [Columnstore memory optimizations](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md) (Otimizações de memória de columnstore).

- Para garantir que o utilizador de carregamento tem memória suficiente para alcançar as velocidades de compressão máximas, utilize utilizadores de carregamento que sejam membros de uma classe de recursos média ou grande.
- Carregue linhas suficientes para preencher completamente rowgroups novos. Durante um carregamento em massa, cada 1 048 576 linhas são comprimidas diretamente para o columnstore como um rowgroup completo. Carregamentos com menos de 102,400 linhas enviam as linhas para o deltastore onde as linhas são guardadas num índice de árvore b.

> [!NOTE]
> Se carregar poucas filas, podem ir todos para a deltastore e não ficar imediatamente comprimidos no formato de loja de colunas.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumente o tamanho do lote ao utilizar a API da SqLBulkCopy ou do BCP

O carregamento com a declaração COPY proporcionará a maior produção com piscinas SQL dedicadas. Se não puder utilizar o COPY para carregar e tiver de utilizar a [API da SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [do BCP,](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)deve considerar o aumento do tamanho do lote para uma melhor produção.

> [!TIP]
> Um tamanho de lote entre 100 K e 1M é a linha de base recomendada para determinar a capacidade ideal do tamanho do lote.

## <a name="handling-loading-failures"></a>Processar falhas de carregamento

Uma carga que utilize uma tabela externa pode falhar com o erro *"Query aborted-- the maximum reject threshold was reached while reading from an external source"* (“Consulta abortada. O limiar de rejeição máximo foi atingido ao ler a partir de uma origem externa”). Esta mensagem indica que os dados externos contém registos desatualizados.

Um registo de dados é considerado sujo se satisfaça uma das seguintes condições:

- Os tipos de dados e o número de colunas não correspondem às definições da coluna da tabela externa.
- Os dados não estão em conformidade com o formato de ficheiro externo especificado.

Para corrigir os registos desatualizados, confirme que as definições de tabela externa e de formato de ficheiro externo estão corretas e que os dados externos estão em conformidade com estas definições.

Se um subconjunto de registos de dados externos estiver sujo, pode optar por rejeitar estes registos para as suas consultas utilizando as opções de rejeição em [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Inserir dados na tabela de produção

Um carregamento único para uma pequena tabela com uma instrução [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ou mesmo um recarregamento periódico de uma consulta pode ajustar-se às suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, as inserções individuais não são tão eficientes como os carregamentos em massa.

Se tiver milhares ou mais de inserções individuais durante o dia, junte-as para poder carregá-las em massa.  Desenvolva os seus processos de modo a que anexem as inserções individuais a um ficheiro e crie outro processo que o carregue periodicamente.

## <a name="creating-statistics-after-the-load"></a>Criação de estatísticas após o carregamento

Para melhorar desempenho das consultas, é importante criar estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou poderão ocorrer alterações substanciais nos dados. A criação de estatísticas pode ser feita manualmente ou pode [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Para uma explicação detalhada das estatísticas, consulte [Estatísticas](sql-data-warehouse-tables-statistics.md). O exemplo a seguir mostra como criar manualmente estatísticas em cinco colunas da tabela Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Rode as teclas de armazenamento (PolyBase)

É boa prática de segurança alterar a chave de acesso ao seu armazenamento de blobs regularmente. Tem duas chaves de armazenamento para a conta de armazenamento de blobs, que lhe permite efetuar a transição das chaves.

Para alternar as chaves da conta de Armazenamento do Azure:

Para cada conta de armazenamento cuja chave mudou, emita [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

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

- Para saber mais sobre a declaração COPY ou PolyBase ao conceber um processo de extrato, carga e transformação (ELT), consulte [Design ELT para Azure Synapse Analytics](design-elt-data-loading.md).
- Para um tutorial de carregamento, [utilize a declaração COPY para carregar os dados do armazenamento da bolha Azure para o Synapse SQL](load-data-from-azure-blob-storage-using-polybase.md).
- Para monitorizar o carregamento de dados, veja [Monitor your workload using DMVs](sql-data-warehouse-manage-monitor.md) (Monitorizar a sua carga de trabalho com redes de perímetro).
