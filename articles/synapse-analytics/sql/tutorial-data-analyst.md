---
title: Tutorial de Analista de Dados - Use sQL on-demand (pré-visualização) para analisar Conjuntos de Dados Abertos Azure no Estúdio Azure Synapse (pré-visualização)
description: Neste tutorial, você aprenderá a realizar facilmente a análise de dados exploratórios combinando diferentes Conjuntos de Dados Abertos Azure usando SQL on-demand (pré-visualização) e visualizar os resultados no Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423230"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Utilize o SQL on-demand (pré-visualização) para analisar os Conjuntos de Dados Abertos do Azure e visualizar os resultados no Azure Synapse Studio (pré-visualização)

Neste tutorial, aprende-se a realizar análises de dados exploratórios, combinando diferentes Conjuntos de Dados Abertos do Azure utilizando a procura do SQL e, em seguida, visualizando os resultados no Azure Synapse Studio.

Em particular, você analisa o conjunto de dados de táxi da cidade de [Nova Iorque (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) que inclui datas/horas de recolha e entrega, locais de recolha e entrega, distâncias de viagem, tarifas item, tipos de tarifas, tipos de pagamento e contagens de passageiros reportadas pelo condutor.

O foco da análise é encontrar tendências nas mudanças no número de passeios de táxi ao longo do tempo. Você analisa outros dois Conjuntos de Dados Abertos Azure ([Feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) e [Dados meteorológicos](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) para entender os outliers em número de passeios de táxi.

## <a name="create-credentials"></a>Criar credenciais

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Inferência automática do esquema

Uma vez que os dados são armazenados em formato de ficheiro Parquet, a inferência automática do esquema está disponível, para que se possa facilmente consultar os dados sem a necessidade de listar os tipos de dados de todas as colunas nos ficheiros. Além disso, pode-se utilizar o mecanismo de coluna virtual e a função de filepath para filtrar um determinado subconjunto de ficheiros.

Vamos primeiro familiarizar-nos com os dados do Táxi de NYC executando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

O seguinte mostra o resultado dos dados do táxi de NYC:

![resultado snippet](./media/tutorial-data-analyst/1.png)

Da mesma forma, podemos consultar o conjunto de dados de feriados usando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

O seguinte mostra o resultado do conjunto de dados das férias públicas:

![resultado snippet 2](./media/tutorial-data-analyst/2.png)

Por último, também podemos consultar o conjunto de dados meteorológicos utilizando a seguinte consulta:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

O seguinte mostra o resultado do conjunto de dados meteorológicos:

![resultado snippet 3](./media/tutorial-data-analyst/3.png)

Pode saber mais sobre o significado das colunas individuais nas descrições dos conjuntos de dados de táxi, [feriados e](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) [dados](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) meteorológicos de [NYC.](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Séries temporais, sazonalidade e análise sueste

Você pode facilmente resumir o número anual de passeios de táxi usando a seguinte consulta:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

O seguinte mostra o resultado para o número anual de passeios de táxi:

![resultado snippet 4](./media/tutorial-data-analyst/4.png)

Os dados podem ser visualizados no Estúdio Synapse, mudando de vista tabela para gráfico. Pode escolher entre diferentes tipos de gráficos (Área, Bar, Coluna, Linha, Torta e Dispersão). Neste caso, vamos traçar gráfico de colunacom coluna de categoria definida para "current_year":

![visualização do resultado 5](./media/tutorial-data-analyst/5.png)

A partir desta visualização, uma tendência de diminuição do número de passeios ao longo dos anos pode ser claramente vista, presumivelmente devido a um recente aumento da popularidade das empresas de partilha de boleias.

> [!NOTE]
> No momento da escrita deste tutorial, os dados relativos a 2019 estão incompletos, pelo que há uma enorme queda em várias viagens para esse ano.

Em seguida, vamos focar a nossa análise num único ano, por exemplo, 2016. A seguinte consulta retorna diariamente número de passeios durante esse ano:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

O seguinte mostra o resultado para esta consulta:

![resultado snippet 6](./media/tutorial-data-analyst/6.png)

Mais uma vez, podemos facilmente visualizar dados através da traçação do gráfico de Colunacom a coluna de categoria "current_day" e coluna Legend (série) "rides_per_day".

![visualização do resultado 7](./media/tutorial-data-analyst/7.png)

A partir do enredo, pode observar-se que há um padrão semanal, com o pico de sábado. Durante os meses de verão, há menos passeios de táxi devido ao período de férias. No entanto, também há algumas quedas significativas no número de passeios de táxi sem um padrão claro quando e por que ocorrem.

Em seguida, vamos ver se essas gotas estão potencialmente correlacionadas com feriados, juntando-se a passeios de táxi de NYC com o conjunto de dados de feriados:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![visualização do resultado 8](./media/tutorial-data-analyst/8.png)

Desta vez, queremos destacar o número de passeios de táxi durante os feriados. Para o efeito, escolheremos "nenhum" para a coluna de categoria e "rides_per_day", e "férias" como colunas Legend (série).

![visualização do resultado 9](./media/tutorial-data-analyst/9.png)

A partir do lote, pode-se ver claramente que durante os feriados públicos uma série de passeios de táxi é menor. No entanto, continua a haver uma queda inexplicável a 23 de janeiro. Vamos verificar o tempo em NYC nesse dia consultando o conjunto de dados meteorológicos:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![visualização do resultado 10](./media/tutorial-data-analyst/10.png)

Os resultados da consulta indicam que a queda em vários passeios de táxi deveu-se ao:

- nevão naquele dia em NYC, como havia neve pesada (~30 cm)
- estava frio (temperatura abaixo de zero graus Celsius)
- e ventoso (~10m/s)

Este tutorial mostrou como o analista de dados pode realizar rapidamente a análise de dados exploratórios, combinar facilmente diferentes conjuntos de dados usando a procura do SQL e visualizar os resultados usando o Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes

Reveja o [Connect SQL on-demand para Power BI Desktop & criar](tutorial-connect-power-bi-desktop.md) artigo de relatório para aprender a ligar o SQL on-demand ao Power BI Desktop e criar relatórios.
 
