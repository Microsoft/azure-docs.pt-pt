---
title: Melhores práticas para SQL on demand (pré-visualização)
description: Recomendações e boas práticas que deve saber quando trabalha com SQL a pedido (pré-visualização).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7bebfeba6da1493557d51777ba8438747e160750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476279"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Melhores práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de boas práticas para usar SQL on demand (pré-visualização). SQL on demand é um recurso na Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

O SQL on demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão. Assim, todos os ficheiros que os alvos de consulta são externos ao SQL on-demand. Tudo relacionado com a leitura de ficheiros de armazenamento pode ter um impacto no desempenho da consulta.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Coloque a sua conta de armazenamento Azure e SQL a pedido

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido do SQL. As contas de armazenamento e os pontos finais a provisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para obter um melhor desempenho, se aceder a outras contas de armazenamento com SQL a pedido, certifique-se de que estão na mesma região. Se não estiverem na mesma região, haverá um aumento da latência para a transferência de rede de dados entre a região remota e a região do ponto final.

## <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento Azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. O estrangulamento de armazenamento ocorre quando o IOPS combinado ou a produção gerada por aplicações, serviços e carga de trabalho a pedido sql excedem os limites da conta de armazenamento. Como resultado, você vai experimentar um efeito negativo significativo no desempenho da consulta.

Quando o estrangulamento é detetado, o SQL on-demand tem manuseamento incorporado para resolvê-lo. A SQL on demand fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução ótima de consultas, não exlinhe a conta de armazenamento com outras cargas de trabalho durante a execução de consultas.

## <a name="prepare-files-for-querying"></a>Preparar ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV e JSON em Parquet. Parquet é um formato colunar. Por ser comprimido, os seus tamanhos de ficheiro são menores do que os ficheiros CSV ou JSON que contêm os mesmos dados. A SQL a pedido necessitará de menos tempo e menos pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiará de o dividir em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo dos 10 GB.
- É melhor ter ficheiros igualmente dimensionados para um único caminho OPENROWSET ou uma LOCALIZAÇÃO DE tabela externa.
- Partition seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros. Consulte [o nome de ficheiro e as funções de filepa para direcionar as divisórias específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Empurre os wildcards para níveis mais baixos no caminho

Pode utilizar wildcards no seu caminho para [consultar vários ficheiros e pastas](query-data-storage.md#query-multiple-files-or-folders). SQL lista ficheiros na sua conta de armazenamento, a partir do primeiro * utilizando a API de armazenamento. Elimina ficheiros que não correspondem ao caminho especificado. A redução da lista inicial de ficheiros pode melhorar o desempenho se houver muitos ficheiros que correspondam ao caminho especificado até ao primeiro wildcard.

## <a name="use-appropriate-data-types"></a>Utilize tipos de dados apropriados

Os tipos de dados que utiliza na sua consulta afetam o desempenho. Pode obter um melhor desempenho se seguir estas orientações: 

- Utilize o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor do caracteres for de 30 caracteres, utilize um tipo de dado de caracteres de comprimento 30.
  - Se todos os valores da coluna de caracteres forem de tamanho fixo, utilize **char** ou **nchar**. Caso contrário, utilize **varchar** ou **nvarchar**.
  - Se o valor máximo da coluna inteiro for de 500, use **a pequena tonalidade** porque é o menor tipo de dados que pode acomodar este valor. Pode encontrar gamas de dados inteiros [neste artigo.](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)
- Se possível, utilize **varchar** e **char** em vez de **nvarchar** e **nchar**.
- Utilize tipos de dados inteiros, se possível. As operações SORT, JOIN e GROUP BY completam-se mais rapidamente em inteiros do que em dados de caracteres.
- Se estiver a utilizar inferência de esquema, [verifique os tipos de dados inferidos](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

[A inferência de Schema](query-parquet-files.md#automatic-schema-inference) ajuda-o a escrever rapidamente consultas e a explorar dados sem conhecer esquemas de ficheiros. O custo desta conveniência é que os tipos de dados inferidos são maiores do que os tipos reais de dados. Isto acontece quando não há informação suficiente nos ficheiros de origem para garantir que o tipo de dados apropriado é usado. Por exemplo, os ficheiros Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres. Assim, o SQL on-demand infere-o como varchar (8000).

Pode utilizar [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) para verificar os tipos de dados resultantes da sua consulta.

O exemplo a seguir mostra como pode otimizar tipos de dados inferidos. Este procedimento é utilizado para mostrar os tipos de dados inferidos: 
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

Aqui está o conjunto de resultados:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|data 2(7)|8|
|0|3|passenger_count|int|4|

Depois de conhecer os tipos de dados inferidos para a consulta, pode especificar os tipos de dados apropriados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Use funções de nome de ficheiro e de filepa para direcionar as divisórias específicas

Os dados são muitas vezes organizados em divisórias. Pode instruir o SQL a pedido para consultar determinadas pastas e ficheiros. Ao fazê-lo, reduzirá o número de ficheiros e a quantidade de dados que a consulta necessita para ler e processar. Um bónus adicional é que conseguirá um melhor desempenho.

Para obter mais informações, leia sobre as funções [de nome de ficheiros](query-data-storage.md#filename-function) e [de filepas](query-data-storage.md#filepath-function) e consulte os exemplos para [consultar ficheiros específicos](query-specific-files.md).

> [!TIP]
> Elenco sempre os resultados das funções de datata de ficheiro e nome de ficheiros para os tipos de dados apropriados. Se utilizar tipos de dados de caracteres, certifique-se de que utiliza o comprimento adequado.

> [!NOTE]
> As funções utilizadas para a eliminação de divisórias, filepath e filename, não são atualmente suportadas para tabelas externas, além das criadas automaticamente para cada tabela criada em Apache Spark para Azure Synapse Analytics.

Se os seus dados armazenados não forem divididos, considere parti-os. Desta forma, pode usar estas funções para otimizar consultas que visam esses ficheiros. Quando consulta [as tabelas Apache Spark para Azure Synapse](develop-storage-files-spark-tables.md) a partir de SQL a pedido, a consulta destinar-se-á automaticamente apenas aos ficheiros necessários.

## <a name="use-parser_version-20-to-query-csv-files"></a>Utilize PARSER_VERSION 2.0 para consultar ficheiros CSV

Pode utilizar um analisador otimizado para o desempenho quando consultar ficheiros CSV. Para mais detalhes, consulte [PARSER_VERSION.](develop-openrowset.md)

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

[O CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis no SQL on demand. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta os resultados da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar peças de consultas frequentemente utilizadas, como tabelas de referência juntas, a um novo conjunto de ficheiros. Em seguida, pode juntar-se a esta única tabela externa em vez de repetir junções comuns em múltiplas consultas.

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta atingir esta tabela externa, resultando numa melhoria do desempenho.

## <a name="azure-ad-pass-through-performance"></a>Performance de passagem AZURE AD

SQL on-demand permite-lhe aceder a ficheiros armazenados utilizando credenciais de acesso ao Azure Ative Directory (Azure AD) ou SAS. Você pode experimentar um desempenho mais lento com Azure AD Pass-through do que você iria com SAS.

Se precisar de um melhor desempenho, tente usar credenciais SAS para aceder ao armazenamento até que o desempenho do Azure AD Pass-through seja melhorado.

## <a name="next-steps"></a>Passos seguintes

Reveja o artigo [de resolução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para encontrar soluções para problemas comuns. Se você está trabalhando com piscinas SQL em vez de SQL on demand, consulte [as melhores práticas para piscinas SQL](best-practices-sql-pool.md) para orientação específica.
