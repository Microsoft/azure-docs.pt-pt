---
title: Boas práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics
description: Recomendações e boas práticas que deve conhecer enquanto trabalha com a SQL on-demand (pré-visualização).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a1a33404982b16e458e97aaf9959ff5dd52d1cce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198888"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Boas práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, encontrará uma coleção de boas práticas para utilizar a SQL a pedido (pré-visualização). SQL on-demand é um recurso adicional dentro da Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

A SQL on-demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão. Como tal, todos os ficheiros que os alvos de consulta são externos ao SQL a pedido. Tudo relacionado com a leitura de ficheiros do armazenamento pode ter impacto no desempenho da consulta.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Conta de Armazenamento Colocate Azure e SQL a pedido

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido da SQL. As contas de armazenamento e os pontos finais aprovisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para um desempenho ótimo, se aceder a outras contas de armazenamento com sql on-demand, certifique-se de que estão na mesma região. Se não estiverem na mesma região, haverá um aumento da latência para a transferência de rede dos dados entre as regiões remotas e finais.

## <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. O estrangulamento do armazenamento ocorre quando o IOPS combinado ou a entrada gerada por aplicações, serviços e carga de trabalho a pedido da SQL excedem os limites da conta de armazenamento. Como resultado, você vai experimentar um efeito negativo significativo no desempenho da consulta.

Uma vez detetada a aceleração, a SQL on-demand tem um manuseamento incorporado deste cenário. A SQL a pedido fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução de consulta ideal, não deve sublinhar a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Prepare ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV e JSON em Parquet - Parquet é formato colunaar. Uma vez que é comprimido, os seus tamanhos de ficheirosão menores do que os ficheiros CSV ou JSON com os mesmos dados. A SQL a pedido necessitará de menos tempo e pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiar-se-á de dividi-lo em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo de 10 GB.
- É melhor ter ficheiros de tamanho igual para um único caminho OPENROWSET ou uma localização de mesa externa.
- Partifique os seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros - verifique funções de nome de [ficheiro e de ficheiros para direcionar divisórias específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Empurre os wildcards para níveis mais baixos no caminho

Pode utilizar wildcards no seu caminho para [consultar vários ficheiros e pastas](develop-storage-files-overview.md#query-multiple-files-or-folders). A SQL lista ficheiros na sua conta de armazenamento a partir do primeiro * utilizando a API de armazenamento e elimina ficheiros que não correspondem ao caminho especificado. A redução da lista inicial de ficheiros pode melhorar o desempenho se existirem muitos ficheiros que correspondem ao caminho especificado até ao primeiro wildcard.

## <a name="use-appropriate-data-types"></a>Utilizar tipos de dados adequados

Os tipos de dados utilizados na sua consulta afetam o desempenho. Pode obter um melhor desempenho se: 

- Utilize o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor do caracteres for de 30 caracteres, utilize o tipo de comprimento 30 do tipo de dados de caracteres.
  - Se todos os valores da coluna de caracteres forem de tamanho fixo, use char ou nchar. Caso contrário, use varchar ou nvarchar.
  - Se o valor máximo da coluna de inteiro for de 500, utilize o menor tipo de dados que pode acomodar este valor. Pode encontrar [gamas](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)de dados inteiros aqui .
- Se possível, use varchar e char em vez de nvarchar e nchar.
- Utilize tipos de dados baseados em inteiros, se possível. Ordenar, juntar e grupo por operações são realizados mais rapidamente em inteiros do que em dados de caracteres.
- Se estiver a utilizar inferência de esquema, verifique o [tipo de dados inferidos](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

[A inferência do schema](query-parquet-files.md#automatic-schema-inference) ajuda-o a escrever rapidamente consultas e a explorar dados sem saber esquema de ficheiros. Este conforto vem em detrimento de tipos de dados inferidos serem maiores do que realmente são. Acontece quando não há informação suficiente nos ficheiros de origem para garantir que o tipo de dados adequado é utilizado. Por exemplo, os ficheiros Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres e a SQL a pedido infere-a como varchar (8000). 

Pode verificar os tipos de dados resultantes da sua consulta utilizando [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

O exemplo que se segue mostra como pode otimizar tipos de dados inferidos. O procedimento é usado para mostrar tipos de dados inferidos. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Aqui está o conjunto de resultados.

|is_hidden|column_ordinal|nome|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|data2(7)|8|
|0|3|passenger_count|int|4|

Assim que soubermos os tipos de dados inferidos para consulta, podemos especificar os tipos de dados adequados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utilize funções de fileinfo e filepath para direcionar divisórias específicas

Os dados são frequentemente organizados em divisórias. Pode instruir a SQL a pedido para consultar determinadas pastas e ficheiros. Esta função reduzirá o número de ficheiros e a quantidade de dados que a consulta precisa de ler e processar. Um bónus adicional é que conseguirá um melhor desempenho.

Para mais informações, verifique o nome de [ficheiros](develop-storage-files-overview.md#filename-function) e as funções [de path path](develop-storage-files-overview.md#filepath-function) e exemplos sobre como consultar [ficheiros específicos](query-specific-files.md).

> [!TIP]
> Lance sempre o resultado das funções de filepath e fileinfo para tipos de dados adequados. Se utilizar tipos de dados de caracteres, certifique-se de que o comprimento adequado é utilizado.

> [!NOTE]
> As funções utilizadas para a eliminação da partição, o arquivo e o fileinfo, não são atualmente suportadas para tabelas externas diferentes das criadas automaticamente para cada tabela criada em Synapse Spark.

Se os seus dados armazenados não forem divididos, considere dividi-los para que possa usar estas funções para otimizar consultas direcionadas a esses ficheiros. Ao [consultar tabelas de Faíscas divididas](develop-storage-files-spark-tables.md) a pedido da SQL, a consulta irá automaticamente direcionar apenas os ficheiros necessários.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Utilize PARSER_VERSION 2.0 para consulta de ficheiros CSV

Pode utilizar um parser otimizado de desempenho ao consultar ficheiros CSV. Verifique [PARSER_VERSION](develop-openrowset.md) detalhes.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

O [CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis na SQL a pedido. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta os resultados da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar partes frequentemente utilizadas de consultas, como tabelas de referência unidas, para um novo conjunto de ficheiros. Em seguida, você pode se juntar a esta única tabela externa em vez de repetir juntas comuns em múltiplas consultas.

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão automaticamente criadas quando a primeira consulta visa esta tabela externa, resultando num melhor desempenho.

## <a name="aad-pass-through-performance"></a>Desempenho pass-through aAD

A SQL on-demand permite-lhe aceder a ficheiros no armazenamento utilizando a credencial AAD pass-through ou SAS. Poderá experimentar um desempenho mais lento com a passagem do AAD em comparação com a SAS. 

Se precisar de um melhor desempenho, experimente as credenciais SAS para aceder ao armazenamento até que o desempenho de passagem do AAD seja melhorado.

## <a name="next-steps"></a>Passos seguintes

Reveja o artigo de resolução de [problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para questões e soluções comuns. Se você está trabalhando com piscina SQL em vez de SQL on-demand, por favor veja as [melhores práticas para](best-practices-sql-pool.md) o artigo de piscina SQL para orientação específica.
