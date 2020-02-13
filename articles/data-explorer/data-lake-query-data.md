---
title: Consultar dados em Azure Data Lake usando o Azure Data Explorer
description: Saiba como consultar dados em Azure Data Lake usando o Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161754"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Consultar dados em Azure Data Lake usando o Azure Data Explorer

Azure Data Lake Storage é uma solução data Lake altamente escalonável e econômica para análise de Big Data. Combina o poder de um sistema de ficheiros de alto desempenho com escala e economia massivas para ajudá-lo a acelerar o seu tempo para a perceção. Data Lake Storage Gen2 alarga as capacidades de armazenamento de Blob Azure e está otimizado para cargas de trabalho analíticas.
 
O Azure Data Explorer integra-se com o Armazenamento de Azure Blob e o Armazenamento de Lagos De Dados Azure (Gen1 e Gen2), proporcionando acesso rápido, cached e indexado aos dados no lago. Você pode analisar e consultar dados no Lake sem ingestão anterior no Azure Data Explorer. Você também pode consultar dados nativos do Lake ingeridos e indesejados simultaneamente.  

> [!TIP]
> O melhor desempenho de consulta exige a ingestão de dados no Azure Data Explorer. A capacidade de consulta de dados externos sem ingestão prévia só deve ser utilizada para dados ou dados históricos que raramente são consultados. [Otimize o seu desempenho de consulta no lago](#optimize-your-query-performance) para obter os melhores resultados.
 

## <a name="create-an-external-table"></a>Criar uma tabela externa

 > [!NOTE]
 > Atualmente, as contas de armazenamento suportadas são o Armazenamento Azure Blob ou o Armazenamento do Lago Azure Data (Gen1 e Gen2).

1. Utilize o comando `.create external table` para criar uma tabela externa no Azure Data Explorer. Comandos de tabela externos adicionais, tais como `.show`, `.drop`e `.alter` estão documentados em [comandos externos](/azure/kusto/management/externaltables)de tabela.

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * O aumento do desempenho é esperado com particionamento mais granular. Por exemplo, as consultas em tabelas externas com partições diárias terão um desempenho melhor do que as consultas com tabelas particionadas mensais.
    > * Quando você define uma tabela externa com partições, espera-se que a estrutura de armazenamento seja idêntica.
Por exemplo, se a tabela for definida com uma partição DateTime em formato yyyy/MM/dd (predefinido), o caminho do ficheiro de armazenamento URI deve ser *contentor1/yyyy/MM/dd/all_exported_blobs*. 
    > * Se a tabela externa for dividida por uma coluna de data, inclua sempre um filtro de tempo para uma gama fechada na sua consulta (por exemplo, a consulta - `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` - deve ter um melhor desempenho do que este (intervalo aberto) um - `ArchivedProducts | where Timestamp > ago(1h)` ). 
    > * Todos os [formatos de ingestão suportados](ingest-data-overview.md#supported-data-formats) podem ser consultados utilizando tabelas externas.

1. A tabela externa está visível no painel esquerdo da interface do usuário da Web

    ![tabela externa na interface do usuário da Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Criar uma tabela externa com o formato JSON

Você pode criar uma tabela externa com o formato JSON. Para mais informações consulte Comandos de [tabela externos](/azure/kusto/management/externaltables)

1. Utilize o comando `.create external table` para criar uma tabela chamada *ExternalTableJson:*

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. O formato JSON exige uma segunda etapa de criação de mapeamento para colunas, conforme mostrado abaixo. Na seguinte consulta, crie um mapeamento específico json chamado *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Permissões de tabela externa
 
* O usuário do banco de dados pode criar uma tabela externa. O criador da tabela se torna automaticamente o administrador da tabela.
* O administrador de cluster, banco de dados ou tabela pode editar uma tabela existente.
* Qualquer usuário ou leitor de banco de dados pode consultar uma tabela externa.
 
## <a name="query-an-external-table"></a>Consultar uma tabela externa
 
Para consultar uma tabela externa, utilize a função `external_table()` e forneça o nome da tabela como argumento de função. O restante da consulta é a linguagem de consulta Kusto padrão.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Atualmente, não há suporte para o IntelliSense em consultas de tabela externa.

### <a name="query-an-external-table-with-json-format"></a>Consultar uma tabela externa com o formato JSON

Para consultar uma tabela externa com formato json, use a função `external_table()` e forneça o nome da tabela e o nome de mapeamento como argumentos de função. Na consulta abaixo, se o *mapeioNome* não for especificado, será utilizado um mapeamento que criou anteriormente.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Consultar dados externos e ingeridos juntos

Você pode consultar tabelas externas e tabelas de dados ingeridos dentro da mesma consulta. Você [`join`](/azure/kusto/query/joinoperator) ou [`union`](/azure/kusto/query/unionoperator) tabela externa com dados adicionais do Azure Data Explorer, servidores SQL ou outras fontes. Utilize um [`let( ) statement`](/azure/kusto/query/letstatement) para atribuir um nome abreviado a uma referência externa da tabela.

No exemplo abaixo, *os Produtos* é uma tabela de dados ingerida e *a ArchivedProducts* é uma tabela externa que contém dados no Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Consulta *TaxiRides* tabela externa no cluster de ajuda

O conjunto de dados de *amostras TaxiRides* contém dados de táxi da cidade de Nova Iorque da Comissão de [Táxis e Limusines](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)de NYC.

### <a name="create-external-table-taxirides"></a>Criar *taxiRides de* mesa externa 

> [!NOTE]
> Esta secção retrata a consulta usada para criar a tabela externa *TaxiRides* no cluster de *ajuda.* Uma vez que esta tabela já foi criada, pode saltar esta secção e realizar dados de [tabela externa *TaxiRides* ](#query-taxirides-external-table-data). 

1. A seguinte consulta foi usada para criar a tabela externa *TaxiRides* no cluster de ajuda. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. A tabela resultante foi criada no aglomerado de *ajuda:*

    ![Tabela externa TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Consulta *TaxiRides* dados de tabela externa 

Inscreva-se na [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) consultar a tabela externa *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Consulta *TaxiRides* mesa externa sem partição

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na tabela externa *TaxiRides* para retratar passeios para cada dia da semana, em todo o conjunto de dados. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Essa consulta mostra o dia mais ocupado da semana. Como os dados não são particionados, essa consulta pode levar muito tempo para retornar resultados (até vários minutos).

![renderizar consulta não particionada](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Consultar tabela externa do TaxiRides com particionamento 

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na mesa externa *TaxiRides* mostrando tipos de táxi (amarelo ou verde) usados em janeiro de 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Essa consulta usa particionamento, o que otimiza o tempo de consulta e o desempenho. Os filtros de consulta numa coluna dividida (pickup_datetime) e devoluções resultam em poucos segundos.

![renderizar consulta particionada](media/data-lake-query-data/taxirides-with-partition.png)
  
Pode escrever consultas adicionais para executar na tabela externa *TaxiRides* e saber mais sobre os dados. 

## <a name="optimize-your-query-performance"></a>Otimizar o desempenho da consulta

Otimize seu desempenho de consulta no Lake usando as práticas recomendadas a seguir para consultar dados externos. 
 
### <a name="data-format"></a>Formato de dados
 
Use um formato de coluna para consultas analíticas desde o:
* Somente as colunas relevantes para uma consulta podem ser lidas. 
* As técnicas de codificação de coluna podem reduzir significativamente o tamanho dos dados.  
O Azure Data Explorer dá suporte aos formatos de coluna parquet e ORC. O formato parquet é sugerido devido à implementação otimizada. 
 
### <a name="azure-region"></a>Região do Azure 
 
Verifique se os dados externos residem na mesma região do Azure que o cluster de Data Explorer do Azure. Isso reduz o custo e o tempo de busca de dados.
 
### <a name="file-size"></a>Tamanho dos ficheiros
 
O tamanho de arquivo ideal é de centenas de MB (até 1 GB) por arquivo. Evite muitos arquivos pequenos que exigem sobrecarga desnecessária, como o processo de enumeração de arquivos mais lento e o uso limitado do formato de coluna. Observe que o número de arquivos deve ser maior que o número de núcleos de CPU no cluster de Data Explorer do Azure. 
 
### <a name="compression"></a>Compressão
 
Use a compactação para reduzir a quantidade de dados que estão sendo buscados do armazenamento remoto. Para o formato parquet, use o mecanismo de compactação parquet interno que compacta os grupos de colunas separadamente, permitindo que você os leia separadamente. Para validar a utilização do mecanismo de compressão, verifique se os ficheiros são nomeados da seguinte forma: "<filename>.gz.parquet" ou "<filename>.snappy.parquet" em oposição a "<filename>.parquet.gz"). 
 
### <a name="partitioning"></a>Criação de partições
 
Organize seus dados usando partições de "pasta" que permitem que a consulta ignore caminhos irrelevantes. Ao planejar o particionamento, considere o tamanho do arquivo e os filtros comuns em suas consultas, como carimbo de data/hora ou ID do locatário.
 
### <a name="vm-size"></a>Tamanho da VM
 
Selecione SKUs de VM com mais núcleos e maior taxa de transferência de rede (a memória é menos importante). Para mais informações consulte [Selecione o VM SKU correto para o seu cluster Azure Data Explorer](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os dados no Azure Data Lake usando o Data Explorer do Azure. Aprenda a [escrever consultas](write-queries.md) e obtenha informações adicionais dos seus dados.
