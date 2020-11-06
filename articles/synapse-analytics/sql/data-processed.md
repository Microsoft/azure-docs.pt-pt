---
title: Dados processados com piscina SQL sem servidor
description: Este documento descreve como os dados processados são calculados ao consultar dados no armazenamento Azure utilizando o pool SQL sem servidor.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424217"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Dados processados com piscina SQL sem servidor em Azure Synapse Analytics

Os dados tratados são a quantidade de dados temporariamente armazenados no sistema durante a execução de uma consulta e consiste em:

- Quantidade de dados lidos a partir do armazenamento - que inclui:
  - Quantidade de dados lidos ao ler dados
  - Quantidade de dados lidos ao ler metadados (para formatos de ficheiros que contenham metadados, como o Parquet)
- Quantidade de dados em resultados intermédios – dados transferidos entre nós durante a execução de consultas, incluindo transferência de dados para o seu ponto final, em formato não comprimido. 
- Quantidade de dados escritos para armazenamento – se utilizar o CETAS para exportar o seu conjunto de resultados para armazenamento, será cobrado pelos bytes escritos e pela quantidade de dados processados para a parte SELECT do CETAS.

Os ficheiros de leitura do armazenamento são altamente otimizados e utiliza:

- Prefetching - que pode adicionar uma pequena sobrecarga à quantidade de dados lidos. Se uma consulta ler um ficheiro inteiro, não haverá sobrecarga. Se um ficheiro for lido parcialmente, como nas consultas TOP N, um pouco mais de dados serão lidos com pré-correção.
- Parser CSV otimizado – se utilizar PARSER_VERSION='2.0' para ler ficheiros CSV, resultará num aumento ligeiramente aumentado das quantidades de dados lidos a partir do armazenamento.  O parser CSV otimizado lê ficheiros em paralelo em pedaços de tamanho igual. Não há garantias de que os pedaços contenham fileiras inteiras. Para garantir que todas as linhas são analisadas, pequenos fragmentos de pedaços adjacentes também serão lidos, adicionando uma pequena quantidade de sobrecarga.

## <a name="statistics"></a>Estatísticas

O otimizador de piscinas SQL sem servidor baseia-se em estatísticas para gerar planos de execução de consultas ideais. Pode criar estatísticas manualmente ou serão criadas automaticamente por uma piscina SQL sem servidor. De qualquer forma, as estatísticas são criadas executando uma consulta separada que devolve uma coluna específica à taxa de amostra fornecida. Esta consulta tem uma quantidade associada de dados processados.

Se executar a mesma ou qualquer outra consulta que beneficiaria de estatísticas criadas, as estatísticas serão reutilizadas se possível e não haverá dados adicionais processados para a criação de estatísticas.

A criação de estatísticas para uma coluna Parquet resultará na leitura apenas da coluna relevante a partir de ficheiros. A criação de estatísticas para uma coluna CSV resultará na leitura e análise de ficheiros inteiros.

## <a name="rounding"></a>Arredondamento

A quantidade de dados processados será arredondada até ao MB mais próximo por consulta, com um mínimo de 10 MB de dados tratados por consulta.

## <a name="what-is-not-included-in-data-processed"></a>O que não está incluído nos dados tratados

- Metadados de nível de servidor (como logins, funções, credenciais de nível de servidor)
- As bases de dados que cria no seu ponto final, uma vez que essas bases de dados contêm apenas metadados (como utilizadores, funções, esquemas, visualizações, TVFs inline, procedimentos armazenados, credenciais de base de dados, fontes de dados externas, formatos de ficheiros externos, tabelas externas)
  - Se utilizar inferência de esquema, serão lidos fragmentos de ficheiros para inferir nomes de colunas e tipos de dados
- Declarações de DDL exceto CREATE STATISTICS, uma vez que processará dados a partir do armazenamento com base na percentagem de amostra especificada
- Consultas só de metadados

## <a name="reduce-amount-of-data-processed"></a>Reduzir a quantidade de dados processados

Pode otimizar a sua quantidade por consulta de dados processados e obter um melhor desempenho através da partilha e conversão dos seus dados para um formato colunar comprimido como o Parquet.

## <a name="examples"></a>Exemplos

Digamos que há duas tabelas, cada uma com os mesmos dados em cinco colunas de tamanho igual:

- population_csv tabela apoiada por 5 TB de ficheiros CSV
- population_parquet tabela apoiada por 1 TB de ficheiros Parquet – esta tabela é menor do que a anterior, uma vez que Parquet contém dados comprimidos
- very_small_csv tabela apoiada por 100 KB de ficheiros CSV

**#1 de consulta** : SELECT SUM(população) FROM population_csv

Esta consulta irá ler e analisar ficheiros inteiros para obter valores para a coluna da população. Os nós processarão fragmentos desta tabela, a soma populacional de cada fragmento será transferida entre nós, e a soma final será transferida para o seu ponto final. Esta consulta processará 5 TB de dados mais pequenas despesas gerais para a transferência de somas de fragmentos.

**#2 de consulta** : SELECT SUM(população) FROM population_parquet

Consultando formatos comprimidos e orientados para colunas como o Parquet resulta na leitura de menos dados do que na consulta anterior, uma vez que o pool SQL sem servidor irá ler uma única coluna comprimida em vez de todo o ficheiro. Neste caso, ler-se-á 0,2 TB (cinco colunas de tamanho igual, 0,2 TB cada). Os nós processarão fragmentos desta tabela, a soma populacional de cada fragmento será transferida entre nós, e a soma final será transferida para o seu ponto final. Esta consulta processará 0.2 TB mais uma pequena sobrecarga para a transferência de somas de fragmentos.

**#3 de consulta** : SELECT * FROM population_parquet

Esta consulta irá ler todas as colunas e transferir todos os dados em formato não comprimido. Se o formato de compressão for 5:1, processará 6 TB, pois lerá 1 TB + transfer 5 TB de dados não comprimidos.

**#4 de consulta** : SELECT COUNT(*) FROM very_small_csv

Esta consulta vai ler ficheiros inteiros. O tamanho total dos ficheiros armazenados para esta tabela é de 100 KB. Os nós processarão fragmentos desta tabela, a soma de cada fragmento será transferida entre nós, e a soma final será transferida para o seu ponto final. Esta consulta irá processar um pouco mais de 100 KB de dados. A quantidade de dados tratados para esta consulta será arredondada até 10 MB, conforme especificado no [Arredondamento](#rounding).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar as suas consultas para obter desempenho, consulte as [melhores práticas para a piscina SQL sem servidor](best-practices-sql-on-demand.md).
