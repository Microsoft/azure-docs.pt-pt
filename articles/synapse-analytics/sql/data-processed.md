---
title: Dados processados utilizando o pool SQL sem servidor
description: Este documento descreve como a quantidade processada pelos dados é calculada quando consulta dados no seu lago de dados.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381206"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Dados processados através da utilização de pool SQL sem servidor no Azure Synapse Analytics

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

**Consulta 1** : SELECT SUM(população) FROM POPULATION_CSV

Esta consulta lê e analisa ficheiros inteiros para obter valores para a coluna da população. Os nós processam fragmentos desta tabela, e a soma populacional para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa 5 TB de dados mais uma pequena quantia sobre a sobrecarga para a transferência de somas de fragmentos.

**Consulta 2** : SELECT SUM(população) FROM population_parquet

Quando consulta formatos comprimidos e baseados em colunas como o Parquet, menos dados são lidos do que na consulta 1. Você vê este resultado porque a piscina SQL sem servidor lê uma única coluna comprimido em vez de todo o ficheiro. Neste caso, lê-se 0,2 TB. (Cinco colunas de tamanho igual são 0,2 TB cada.) Os nós processam fragmentos desta tabela, e a soma populacional para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa 0.2 TB mais uma pequena quantidade de despesas gerais para a transferência de somas de fragmentos.

**Consulta 3** : SELECIONE * FROM population_parquet

Esta consulta lê todas as colunas e transfere todos os dados num formato não comprimido. Se o formato de compressão for 5:1, então a consulta processa 6 TB porque lê 1 TB e transfere 5 TB de dados não comprimidos.

**Consulta 4** : SELECT COUNT(*) FROM very_small_csv

Esta consulta lê ficheiros inteiros. O tamanho total dos ficheiros armazenados para esta tabela é de 100 KB. Os nós processam fragmentos desta tabela, e a soma para cada fragmento é transferida entre nós. A soma final é transferida para o seu ponto final. 

Esta consulta processa pouco mais de 100 KB de dados. A quantidade de dados tratados para esta consulta é arredondada até 10 MB, conforme especificado na secção [arredondamento](#rounding) deste artigo.

## <a name="next-steps"></a>Próximos passos

Para aprender a otimizar as suas consultas para desempenho, consulte [as melhores práticas para piscina SQL sem servidor.](best-practices-sql-on-demand.md)
