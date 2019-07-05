---
title: Consultar dados no Azure Data Lake com o Explorador de dados do Azure
description: Saiba como consultar dados no Azure Data Lake com o Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453180"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Consultar dados no Azure Data Lake com o Explorador de dados do Azure (pré-visualização)

Armazenamento do Azure Data Lake é uma solução de lake de dados altamente escalonável e econômica para análise de macrodados. Alia o poder de um sistema de ficheiros de elevado desempenho à economia e escala maciça para o ajudar a chegar mais rapidamente às informações que realmente interessam e oferecem valor. A Ger2 de Armazenamento do Data Lake expande as capacidades de Armazenamento de Blobs do Azure e está otimizada para cargas de trabalho de análise.
 
O Explorador de dados do Azure se integra com o armazenamento de Blobs do Azure e Azure Data Lake armazenamento ger2, fornecendo rápida e em cache e indexados acesso aos dados no lake. Pode analisar e consultar dados no lake sem ingestão anterior no Explorador de dados do Azure. Também pode consultar em todos os dados ingeridos e uningested lake nativo em simultâneo.  

> [!TIP]
> O melhor desempenho de consulta exige a ingestão de dados no Explorador de dados do Azure. A capacidade de consultar dados na geração 2 de armazenamento do Azure Data Lake sem ingestão anterior só deve ser utilizada para dados históricos ou dados que raramente são consultados.
 
## <a name="optimize-query-performance-in-the-lake"></a>Otimizar o desempenho de consulta no lake 

* Dados de partição para um desempenho melhorado e a hora de otimizado de consulta.
* Comprima os dados para um melhor desempenho (gzip para melhor compressão, lz4 para um melhor desempenho).
* Utilize o armazenamento de Blobs do Azure ou de geração 2 de armazenamento do Azure Data Lake com a mesma região que o seu cluster do Explorador de dados do Azure. 

## <a name="create-an-external-table"></a>Criar uma tabela externa

1. Utilize o `.create external table` comando para criar uma tabela externa no Explorador de dados do Azure. Tabela externa adicional, tais como os comandos `.show`, `.drop`, e `.alter` estão documentadas na [comandos de tabela externa](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Esta consulta cria partições diárias *container1/yyyy/MM/dd/all_exported_blobs.csv*. Aumentar o desempenho é esperado com mais granular de criação de partições. Por exemplo, consultas em tabelas externas com partições diárias, tal como a acima, terá melhor desempenho do que essas consultas com tabelas particionadas mensais.

    > [!NOTE]
    > Contas de armazenamento atualmente suportados são o armazenamento de Blobs do Azure ou de geração 2 de armazenamento do Azure Data Lake. Formatos de dados atualmente suportados são csv, tsv e txt.

1. A tabela externa está visível no painel do lado esquerdo da interface do Usuário de Web

    ![tabela externa na IU da web](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Permissões de tabela externa
 
* O utilizador de base de dados pode criar uma tabela externa. O criador de tabela torna-se automaticamente o administrador de tabela.
* O cluster, a base de dados ou o administrador de tabela, pode editar uma tabela existente.
* Qualquer utilizador de base de dados ou o leitor pode consultar uma tabela externa.
 
## <a name="query-an-external-table"></a>Consultar uma tabela externa
 
Para consultar uma tabela externa, utilize o `external_table()` funcionar e forneça o nome de tabela como o argumento da função. O resto da consulta é a linguagem de consulta de Kusto padrão.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> O IntelliSense não é atualmente suportado em consultas de tabela externa.

## <a name="query-external-and-ingested-data-together"></a>Consultar dados externos e ingeridos em conjunto

Pode consultar tabelas externas e tabelas de dados ingeridos da mesma consulta. [ `join` ](/azure/kusto/query/joinoperator) Ou [ `union` ](/azure/kusto/query/unionoperator) tabela externa com dados adicionais a partir do Explorador de dados do Azure, servidores SQL ou outras origens. Utilize um [ `let( ) statement` ](/azure/kusto/query/letstatement) para atribuir um nome de um atalho para uma referência de tabela externa.

No exemplo abaixo, *produtos* é uma tabela de dados ingeridos e *ArchivedProducts* é uma tabela externa que contém dados no Azure Data Lake Storage geração 2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Consulta *TaxiRides* tabela externa no cluster de ajuda

O *TaxiRides* conjunto de dados de exemplo contém dados de táxis da cidade de nova York partir [táxis de NYC e Limousine Comissão](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Criar tabela externa *TaxiRides* 

> [!NOTE]
> Esta secção descreve a consulta usada para criar o *TaxiRides* tabela externa na *ajudar* cluster. Uma vez que esta tabela já foi criada pode ignorar esta secção e efetuar [consulta *TaxiRides* dados de tabela externa](#query-taxirides-external-table-data). 

1. A seguinte consulta foi utilizada para criar tabela externa *TaxiRides* no cluster de ajuda. 

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
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. A tabela resultante foi criada no *ajudar* cluster:

    ![Tabela externa de TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Consulta *TaxiRides* dados de tabela externa 

Inicie sessão no [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) para consultar o *TaxiRides* tabela externa. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Consulta *TaxiRides* tabela externa sem a criação de partições

[Executar esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na tabela externa *TaxiRides* para retratar passes para cada dia da semana, em todo o conjunto de dados. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Esta consulta mostra o mais ocupado dia da semana. Uma vez que os dados não for particionados, esta consulta pode demorar muito tempo para devolver resultados (até vários minutos).

![compor a consulta não-particionada](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Consultar TaxiRides tabela externa com a criação de partições 

[Executar esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na tabela externa *TaxiRides* que mostre a táxis de tipos do cab (amarelo ou verde) utilizado em Janeiro de 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Esta consulta utiliza a criação de partições, que otimiza o desempenho e de tempo de consulta. A consulta filtra numa coluna particionada (pickup_datetime) e devolve resultados dentro de alguns segundos.

![compor a consulta particionada](media/data-lake-query-data/taxirides-with-partition.png)
  
Pode escrever consultas adicionais para executar na tabela externa *TaxiRides* e saiba mais sobre os dados. 

## <a name="next-steps"></a>Passos Seguintes

Consulte os dados no Azure Data Lake com o Explorador de dados do Azure. Aprenda a [escrever consultas](write-queries.md) e obter informações adicionais a partir dos seus dados.