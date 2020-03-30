---
title: Dados de consulta no Lago de Dados Azure usando o Azure Data Explorer
description: Saiba como consultar dados no Lago de Dados Azure utilizando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161754"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Dados de consulta no Lago de Dados Azure usando o Azure Data Explorer

O Azure Data Lake Storage é uma solução altamente escalável e rentável para a análise de big data. Combina o poder de um sistema de ficheiros de alto desempenho com escala e economia massivas para ajudá-lo a acelerar o seu tempo para a perceção. Data Lake Storage Gen2 alarga as capacidades de armazenamento de Blob Azure e está otimizado para cargas de trabalho analíticas.
 
O Azure Data Explorer integra-se com o Armazenamento de Azure Blob e o Armazenamento de Lagos De Dados Azure (Gen1 e Gen2), proporcionando acesso rápido, cached e indexado aos dados no lago. Pode analisar e consultar dados no lago sem inserção prévia no Azure Data Explorer. Você também pode consultar dados ingeridos e ingeridos do lago nativo simultaneamente.  

> [!TIP]
> O melhor desempenho da consulta requer a ingestão de dados no Azure Data Explorer. A capacidade de consulta de dados externos sem ingestão prévia só deve ser utilizada para dados ou dados históricos que raramente são consultados. [Otimize o seu desempenho de consulta no lago](#optimize-your-query-performance) para obter os melhores resultados.
 

## <a name="create-an-external-table"></a>Criar uma tabela externa

 > [!NOTE]
 > Atualmente, as contas de armazenamento suportadas são o Armazenamento Azure Blob ou o Armazenamento do Lago Azure Data (Gen1 e Gen2).

1. Utilize `.create external table` o comando para criar uma tabela externa no Azure Data Explorer. Comandos de tabela `.show`externos adicionais tais como, `.drop`, e estão `.alter` documentados em comandos de mesa [externos](/azure/kusto/management/externaltables).

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
    > * Espera-se um maior desempenho com mais partição granular. Por exemplo, consultas sobre mesas externas com divisórias diárias, terão melhor desempenho do que aquelas consultas com mesas divisórias mensais.
    > * Quando se define uma tabela externa com divisórias, espera-se que a estrutura de armazenamento seja idêntica.
Por exemplo, se a tabela for definida com uma partição DateTime em formato yyyy/MM/dd (predefinido), o caminho do ficheiro de armazenamento URI deve ser *contentor1/yyyy/MM/dd/all_exported_blobs*. 
    > * Se a tabela externa for dividida por uma coluna de data, inclua sempre um filtro de `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` tempo para uma gama fechada na `ArchivedProducts | where Timestamp > ago(1h)` sua consulta (por exemplo, a consulta - deve ter um melhor desempenho do que este (intervalo aberto) um - ). 
    > * Todos os [formatos de ingestão suportados](ingest-data-overview.md#supported-data-formats) podem ser consultados utilizando tabelas externas.

1. A tabela externa é visível no painel esquerdo da Web UI

    ![tabela externa na Web UI](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Criar uma tabela externa com formato json

Pode criar uma tabela externa com formato json. Para mais informações consulte Comandos de [tabela externos](/azure/kusto/management/externaltables)

1. Utilize `.create external table` o comando para criar uma tabela chamada *ExternalTableJson:*

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
 
1. O formato Json requer um segundo passo de criação de mapeamento para colunas, como mostrado abaixo. Na seguinte consulta, crie um mapeamento específico json chamado *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Permissões externas de tabela
 
* O utilizador da base de dados pode criar uma tabela externa. O criador da tabela torna-se automaticamente o administrador da tabela.
* O cluster, a base de dados ou o administrador de mesa podem editar uma tabela existente.
* Qualquer utilizador ou leitor de base de dados pode consultar uma tabela externa.
 
## <a name="query-an-external-table"></a>Consulta de uma mesa externa
 
Para consultar uma tabela externa, use a `external_table()` função e forneça o nome da tabela como argumento de função. O resto da consulta é a linguagem padrão da consulta kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> O Intellisense não é atualmente suportado em consultas de mesa externas.

### <a name="query-an-external-table-with-json-format"></a>Consulta de uma tabela externa com formato json

Para consultar uma tabela externa com formato `external_table()` json, use a função e forneça o nome da tabela e o nome de mapeamento como argumentos de função. Na consulta abaixo, se o *mapeioNome* não for especificado, será utilizado um mapeamento que criou anteriormente.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Consulta de dados externos e ingeridos em conjunto

Pode consultar ambas as tabelas externas e ingerir tabelas de dados dentro da mesma consulta. Você [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) ou a tabela externa com dados adicionais do Azure Data Explorer, servidores SQL ou outras fontes. Utilize [`let( ) statement`](/azure/kusto/query/letstatement) um nome abreviado para uma referência externa à tabela.

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

Inscreva-se [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) para consultar a tabela externa *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Consulta *TaxiRides* mesa externa sem partição

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na tabela externa *TaxiRides* para retratar passeios para cada dia da semana, em todo o conjunto de dados. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Esta consulta mostra o dia mais movimentado da semana. Uma vez que os dados não são divididos, esta consulta pode demorar muito tempo a devolver resultados (até vários minutos).

![tornar consulta não dividida](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Consulta TaxiRides mesa externa com partição 

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na mesa externa *TaxiRides* mostrando tipos de táxi (amarelo ou verde) usados em janeiro de 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Esta consulta utiliza a partilha, o que otimiza o tempo e o desempenho da consulta. Os filtros de consulta numa coluna dividida (pickup_datetime) e devoluções resultam em poucos segundos.

![tornar consulta dividida](media/data-lake-query-data/taxirides-with-partition.png)
  
Pode escrever consultas adicionais para executar na tabela externa *TaxiRides* e saber mais sobre os dados. 

## <a name="optimize-your-query-performance"></a>Otimize o seu desempenho de consulta

Otimize o seu desempenho de consulta no lago utilizando as seguintes melhores práticas para consulta de dados externos. 
 
### <a name="data-format"></a>Formato de dados
 
Utilize um formato colunaar para consultas analíticas desde:
* Só podem ser lidas as colunas relevantes para uma consulta. 
* As técnicas de codificação de colunas podem reduzir significativamente o tamanho dos dados.  
O Azure Data Explorer suporta formatos colunares Parquet e ORC. O formato Parquet é sugerido devido à implementação otimizada. 
 
### <a name="azure-region"></a>Região do Azure 
 
Verifique se os dados externos residem na mesma região do Azure que o seu cluster Azure Data Explorer. Isto reduz o tempo de procura de custos e dados.
 
### <a name="file-size"></a>Tamanho dos ficheiros
 
O tamanho ideal do ficheiro é de centenas de Mb (até 1 Gb) por ficheiro. Evite muitos pequenos ficheiros que requerem despesas aéreas desnecessárias, tais como processo de enumeração de ficheiros mais lento e utilização limitada do formato colunaar. Note que o número de ficheiros deve ser maior do que o número de núcleos de CPU no seu cluster Azure Data Explorer. 
 
### <a name="compression"></a>Compressão
 
Utilize a compressão para reduzir a quantidade de dados que estão a ser recolhidos a partir do armazenamento remoto. Para o formato Parquet, utilize o mecanismo interno de compressão parquet que comprime os grupos de colunas separadamente, permitindo-lhe lê-los separadamente. Para validar a utilização do mecanismo de compressão, verifique se os ficheiros são nomeados da seguinte forma: "<filename>.gz.parquet" ou "<filename>.snappy.parquet" em oposição a "<filename>.parquet.gz"). 
 
### <a name="partitioning"></a>Criação de partições
 
Organize os seus dados utilizando divisórias de "pasta" que permitam à consulta saltar caminhos irrelevantes. Ao planear a partilha considere o tamanho do ficheiro e filtros comuns nas suas consultas, tais como carimbo de tempo ou identificação do inquilino.
 
### <a name="vm-size"></a>Tamanho da VM
 
Selecione VM SKUs com mais núcleos e maior suporte de rede (a memória é menos importante). Para mais informações consulte [Selecione o VM SKU correto para o seu cluster Azure Data Explorer](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Passos seguintes

Consulta os seus dados no Lago de Dados Azure utilizando o Azure Data Explorer. Aprenda a [escrever consultas](write-queries.md) e obtenha informações adicionais dos seus dados.
