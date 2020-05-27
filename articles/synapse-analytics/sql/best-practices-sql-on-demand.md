---
title: Boas práticas para SQL a pedido (pré-visualização)
description: Recomendações e boas práticas deve saber quando trabalha com a SQL a pedido (pré-visualização).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 79318ab67ec58ed10520365a366785ea0de41666
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836333"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Boas práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, encontrará uma coleção de boas práticas para utilizar a SQL a pedido (pré-visualização). SQL on-demand é um recurso na Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

A SQL on-demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão. Então, todos os ficheiros que os alvos de consulta são externos à SQL a pedido. Tudo relacionado com a leitura de ficheiros do armazenamento pode ter impacto no desempenho da consulta.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Colocaa a sua conta de armazenamento Azure e a pedido da SQL

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido da SQL. As contas de armazenamento e os pontos finais aprovisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para um desempenho ótimo, se aceder a outras contas de armazenamento com sQL a pedido, certifique-se de que estão na mesma região. Se não estiverem na mesma região, haverá um aumento da latência para a transferência de rede dos dados entre a região remota e a região do ponto final.

## <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. O estrangulamento do armazenamento ocorre quando o IOPS combinado ou a entrada gerada por aplicações, serviços e carga de trabalho a pedido da SQL excedem os limites da conta de armazenamento. Como resultado, você vai experimentar um efeito negativo significativo no desempenho da consulta.

Quando se deteta a aceleração, a SQL on-demand tem um manuseamento incorporado para resolvê-lo. A SQL a pedido fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução de consulta ideal, não sublinhe a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Prepare ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV e JSON em Parquet. Parquet é um formato colunaar. Por estar comprimido, os seus tamanhos de ficheirosão menores do que os ficheiros CSV ou JSON que contêm os mesmos dados. A SQL a pedido necessitará de menos tempo e menos pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiar-se-á de dividi-lo em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo de 10 GB.
- É melhor ter ficheiros de tamanho igual para um único caminho OPENROWSET ou uma localização de mesa externa.
- Partifique os seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros. Consulte [utilize funções de nome de ficheiro e de ficheiros para direcionar divisórias específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Empurre os wildcards para níveis mais baixos no caminho

Pode utilizar wildcards no seu caminho para [consultar vários ficheiros e pastas](develop-storage-files-overview.md#query-multiple-files-or-folders). A SQL lista ficheiros na sua conta de armazenamento, a partir da primeira * utilizando a API de armazenamento. Elimina ficheiros que não correspondem ao caminho especificado. A redução da lista inicial de ficheiros pode melhorar o desempenho se existirem muitos ficheiros que correspondam ao caminho especificado até ao primeiro wildcard.

## <a name="use-appropriate-data-types"></a>Utilizar tipos de dados adequados

Os tipos de dados que utiliza na sua consulta afetam o desempenho. Pode obter um melhor desempenho se seguir estas orientações: 

- Utilize o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo de valor do caracteres for de 30 caracteres, utilize um tipo de comprimento 30 de dados de caracteres.
  - Se todos os valores da coluna de caracteres forem de tamanho fixo, utilize **carvão** ou **nchar**. Caso contrário, utilize **varchar** ou **nvarchar.**
  - Se o valor máximo da coluna de inteiro for de 500, use **menor** por ser o menor tipo de dados que pode acomodar este valor. Pode encontrar gamas de dados inteiros [neste artigo](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Se possível, utilize **varchar** e **char** em vez de **nvarchar** e **nchar**.
- Utilize tipos de dados baseados em inteiros, se possível. SORT, JOIN e GROUP BY operações completam mais rapidamente em inteiros do que em dados de caracteres.
- Se estiver a utilizar inferência de esquema, [verifique os tipos de dados inferidos](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

[A inferência de Schema](query-parquet-files.md#automatic-schema-inference) ajuda-o a escrever rapidamente consultas e a explorar dados sem saber schemas de ficheiros. O custo desta conveniência é que os tipos de dados inferidos são maiores do que os tipos de dados reais. Isto acontece quando não há informação suficiente nos ficheiros de origem para se certificar de que o tipo de dados adequado é usado. Por exemplo, os ficheiros Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres. Assim, a SQL a pedido infere-a como varchar (8000).

Pode utilizar [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) para verificar os tipos de dados resultantes da sua consulta.

O exemplo que se segue mostra como pode otimizar tipos de dados inferidos. Este procedimento é utilizado para mostrar os tipos de dados inferidos: 
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
|0|2|pickup_datetime|data2(7)|8|
|0|3|passenger_count|int|4|

Depois de conhecer os tipos de dados inferidos para a consulta, pode especificar os tipos de dados adequados:

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

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Utilize funções de nome de ficheiro e de path path para direcionar divisórias específicas

Os dados são frequentemente organizados em divisórias. Pode instruir a SQL a pedido para consultar determinadas pastas e ficheiros. Ao fazê-lo reduzirá o número de ficheiros e a quantidade de dados que a consulta precisa de ler e processar. Um bónus adicional é que conseguirá um melhor desempenho.

Para mais informações, leia sobre o nome de [ficheiro](develop-storage-files-overview.md#filename-function) e funções [de pathpath](develop-storage-files-overview.md#filepath-function) e consulte os exemplos para consulta de [ficheiros específicos](query-specific-files.md).

> [!TIP]
> Enumere sempre os resultados das funções de pathpath e nome de ficheiro para tipos de dados adequados. Se utilizar tipos de dados de caracteres, certifique-se de que utiliza o comprimento adequado.

> [!NOTE]
> As funções utilizadas para a eliminação da partição, o pathpath e o nome de ficheiro, não são atualmente suportadas para tabelas externas, além das criadas automaticamente para cada tabela criada em Apache Spark para Azure Synapse Analytics.

Se os seus dados armazenados não forem divididos, considere dividi-lo. Dessa forma, pode utilizar estas funções para otimizar consultas que visam esses ficheiros. Quando [consultada Apache Spark para tabelas Synapse Azure](develop-storage-files-spark-tables.md) a pedido, a consulta irá automaticamente direcionar apenas os ficheiros necessários.

## <a name="use-parser_version-20-to-query-csv-files"></a>Utilize PARSER_VERSION 2.0 para consultar ficheiros CSV

Pode utilizar um parser otimizado para o desempenho quando consultar ficheiros CSV. Para mais detalhes, consulte [PARSER_VERSION.](develop-openrowset.md)

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

O [CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis na SQL a pedido. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta os resultados da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar partes frequentemente utilizadas de consultas, como tabelas de referência unidas, para um novo conjunto de ficheiros. Em seguida, pode juntar-se a esta única tabela externa em vez de repetir juntas comuns em múltiplas consultas.

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão automaticamente criadas quando a primeira consulta visa esta tabela externa, resultando num melhor desempenho.

## <a name="azure-ad-pass-through-performance"></a>Desempenho do Passe-Through da Azure AD

A SQL on-demand permite-lhe aceder a ficheiros armazenados utilizando credenciais de Pass-through ou SAS do Azure Ative Directory (Azure AD). Poderá experimentar um desempenho mais lento com o Azure AD Pass-through do que com o SAS.

Se precisar de um melhor desempenho, tente utilizar credenciais SAS para aceder ao armazenamento até que o desempenho do Azure AD Pass-through seja melhorado.

## <a name="next-steps"></a>Passos seguintes

Reveja o artigo de resolução de [problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para soluções para problemas comuns. Se você está trabalhando com piscinas SQL em vez de SQL on-demand, consulte [as melhores práticas para piscinas SQL](best-practices-sql-pool.md) para orientação específica.
