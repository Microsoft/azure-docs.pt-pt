---
title: Gestão de custos para o conjunto de SQL sem servidor
description: Este documento descreve como gerir o custo do pool SQL sem servidor e como os dados processados são calculados ao consultar dados no armazenamento do Azure.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491589"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Gestão de custos para piscina SQL sem servidor em Azure Synapse Analytics

Este artigo explica como pode estimar e gerir custos para a piscina SQL sem servidor em Azure Synapse Analytics:
- Estimativa da quantidade de dados processados antes de emitir uma consulta
- Utilize a função de controlo de custos para definir o orçamento

Entenda que os custos para a piscina SQL sem servidor em Azure Synapse Analytics são apenas uma parte dos custos mensais na sua conta Azure. Se estiver a utilizar outros serviços Azure, é cobrado por todos os serviços e recursos da Azure utilizados na sua subscrição Azure, incluindo os serviços de terceiros. Este artigo explica como planear e gerir custos para a piscina SQL sem servidor em Azure Synapse Analytics.

## <a name="data-processed"></a>Dados processados

*Os dados processados* são a quantidade de dados que o sistema armazena temporariamente enquanto uma consulta é executada. Os dados tratados consistem nas seguintes quantidades:

- Quantidade de dados lidos a partir do armazenamento. Este valor inclui:
  - Dados lidos ao ler dados.
  - Os dados lêem durante a leitura dos metadados (para formatos de ficheiros que contenham metadados, como o Parquet).
- Quantidade de dados em resultados intermédios. Estes dados são transferidos entre nós enquanto a consulta decorre. Inclui a transferência de dados para o seu ponto final, num formato não comprimido. 
- Quantidade de dados escritos para armazenamento. Se utilizar o CETAS para exportar o seu conjunto de resultados para armazenamento, então a quantidade de dados escritos é adicionada à quantidade de dados processados para a parte SELECT do CETAS.

A leitura de ficheiros de armazenamento é altamente otimizada. O processo utiliza:

- Prefetching, que pode adicionar alguma sobrecarga à quantidade de dados lidos. Se uma consulta lê um ficheiro inteiro, então não há sobrecarga. Se um ficheiro for lido parcialmente, como nas consultas TOP N, então um pouco mais de dados são lidos utilizando a pré-correção.
- Um parser de valor separado de vírgula otimizado (CSV). Se utilizar PARSER_VERSION='2.0' para ler ficheiros CSV, então as quantidades de dados lidos a partir do armazenamento aumentam ligeiramente. Um parser CSV otimizado lê ficheiros em paralelo, em pedaços de tamanho igual. Pedaços não contêm necessariamente fileiras inteiras. Para garantir que todas as linhas são analisadas, o parser CSV otimizado também lê pequenos fragmentos de pedaços adjacentes. Este processo adiciona uma pequena quantidade de sobrecarga.

## <a name="statistics"></a>Estatísticas

O otimizador de piscinas SQL sem servidor baseia-se em estatísticas para gerar planos de execução de consultas ideais. Pode criar estatísticas manualmente. Caso contrário, a piscina SQL sem servidor cria-as automaticamente. De qualquer forma, as estatísticas são criadas executando uma consulta separada que devolve uma coluna específica a uma taxa de amostra fornecida. Esta consulta tem uma quantidade associada de dados processados.

Se executar a mesma ou qualquer outra consulta que beneficiaria de estatísticas criadas, então as estatísticas são reutilizadas se possível. Não há dados adicionais processados para a criação de estatísticas.

Quando as estatísticas são criadas para uma coluna Parquet, apenas a coluna relevante é lida a partir de ficheiros. Quando as estatísticas são criadas para uma coluna CSV, ficheiros inteiros são lidos e analisados.

## <a name="rounding"></a>Arredondamento

A quantidade de dados processados é arredondada para o MB mais próximo por consulta. Cada consulta tem um mínimo de 10 MB de dados processados.

## <a name="what-data-processed-doesnt-include"></a>Que dados processados não incluem

- Metadados de nível de servidor (como logins, funções e credenciais de nível de servidor).
- Bases de dados que cria no seu ponto final. Essas bases de dados contêm apenas metadados (como utilizadores, funções, esquemas, pontos de vista, funções de valor de tabelas inline [TVFs], procedimentos armazenados, credenciais de base de dados, fontes de dados externas, formatos de ficheiros externos e tabelas externas).
  - Se utilizar inferência de esquema, os fragmentos de ficheiros são lidos para inferir nomes de colunas e tipos de dados, e a quantidade de leitura de dados é adicionada à quantidade de dados processados.
- Declarações de linguagem de definição de dados (DDL), com exceção da declaração DE ESTATÍSTICAS CREATE porque processa dados a partir do armazenamento com base na percentagem de amostra especificada.
- Consultas só de metadados.

## <a name="reducing-the-amount-of-data-processed"></a>Redução da quantidade de dados processados

Pode otimizar a sua quantidade por consulta de dados processados e melhorar o desempenho através da partilha e conversão dos seus dados para um formato baseado em colunas comprimidos como o Parquet.

## <a name="examples"></a>Exemplos

Imagine três mesas.

- A tabela population_csv é apoiada por 5 TB de ficheiros CSV. Os ficheiros são organizados em cinco colunas de tamanho igual.
- A tabela population_parquet tem os mesmos dados que a tabela population_csv. É apoiado por 1 TB de arquivos Parquet. Esta tabela é menor do que a anterior porque os dados são comprimidos no formato Parquet.
- A tabela very_small_csv é apoiada por 100 KB de ficheiros CSV.

**Consulta 1**: SELECT SUM(população) FROM POPULATION_CSV

Esta consulta lê e analisa ficheiros inteiros para obter valores para a coluna da população. Os nós processam fragmentos desta tabela, e a soma populacional para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa 5 TB de dados mais uma pequena quantia sobre a sobrecarga para a transferência de somas de fragmentos.

**Consulta 2**: SELECT SUM(população) FROM population_parquet

Quando consulta formatos comprimidos e baseados em colunas como o Parquet, menos dados são lidos do que na consulta 1. Você vê este resultado porque a piscina SQL sem servidor lê uma única coluna comprimido em vez de todo o ficheiro. Neste caso, lê-se 0,2 TB. (Cinco colunas de tamanho igual são 0,2 TB cada.) Os nós processam fragmentos desta tabela, e a soma populacional para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa 0.2 TB mais uma pequena quantidade de despesas gerais para a transferência de somas de fragmentos.

**Consulta 3**: SELECIONE * FROM population_parquet

Esta consulta lê todas as colunas e transfere todos os dados num formato não comprimido. Se o formato de compressão for 5:1, então a consulta processa 6 TB porque lê 1 TB e transfere 5 TB de dados não comprimidos.

**Consulta 4**: SELECT COUNT(*) FROM very_small_csv

Esta consulta lê ficheiros inteiros. O tamanho total dos ficheiros armazenados para esta tabela é de 100 KB. Os nós processam fragmentos desta tabela, e a soma para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa pouco mais de 100 KB de dados. A quantidade de dados tratados para esta consulta é arredondada até 10 MB, conforme especificado na secção [arredondamento](#rounding) deste artigo.

## <a name="cost-control"></a>Controlo de custos

A funcionalidade de controlo de custos no pool SQL sem servidor permite-lhe definir o orçamento para a quantidade de dados processados. Pode definir o orçamento em Tuberculose de dados processados por um dia, semana e mês. Ao mesmo tempo, pode ter um ou mais orçamentos definidos. Para configurar o controlo de custos para a piscina SQL sem servidor, pode utilizar o Synapse Studio ou o T-SQL.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>Configure o controlo de custos para a piscina SQL sem servidor no Synapse Studio
 
Para configurar o controlo de custos para a piscina SQL sem servidor no Synapse Studio, navegue para Gerir o item no menu à esquerda, do que selecionar o item da piscina SQL em piscinas Analytics. Ao pairar na piscina SQL sem servidor, irá notar um ícone para controlo de custos - clique neste ícone.

![Navegação de controlo de custos](./media/data-processed/cost-control-menu.png)

Assim que clicar no ícone de controlo de custos, aparecerá uma barra lateral:

![Configuração do controlo de custos](./media/data-processed/cost-control-sidebar.png)

Para definir um ou mais orçamentos, clique primeiro no botão de rádio para um orçamento que pretende definir, do que insira o valor inteiro na caixa de texto. Unidade para o valor são TBs. Uma vez configurados os orçamentos que queria, clique no botão de aplicação na parte inferior da barra lateral. É isso, o seu orçamento está definido.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>Configure o controlo de custos para a piscina SQL sem servidor em T-SQL

Para configurar o controlo de custos para a piscina SQL sem servidor em T-SQL, é necessário executar um ou mais dos seguintes procedimentos armazenados.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Para ver a configuração atual execute a seguinte declaração T-SQL:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Para ver quantos dados foram processados durante o dia, semana ou mês, execute a seguinte declaração T-SQL:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar as suas consultas para desempenho, consulte [as melhores práticas para piscina SQL sem servidor.](best-practices-sql-on-demand.md)
