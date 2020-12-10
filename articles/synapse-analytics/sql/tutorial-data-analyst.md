---
title: 'Tutorial: Use piscina SQL sem servidor para analisar conjuntos de dados Azure Open no Azure Synapse Studio'
description: Este tutorial mostra-lhe como realizar facilmente a análise de dados exploratórios combinando diferentes conjuntos de dados Azure Open usando piscina SQL sem servidor e visualizar os resultados no Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d37597f8667c461e8d61f8214483f57eb702c2a0
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007556"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Tutorial: Explore e analise lagos de dados com piscina SQL sem servidor

Neste tutorial, aprende-se a realizar análises exploratórias de dados. Você combinará diferentes conjuntos de dados Azure Open usando piscina SQL sem servidor. Em seguida, visualizará os resultados no Synapse Studio para Azure Synapse Analytics.

A função OPENROWSET (BULK...) permite-lhe aceder a ficheiros no Azure Storage. A função [OPENROWSET](develop-openrowset.md) lê o conteúdo de uma fonte de dados remoto (por exemplo, ficheiro) e devolve o conteúdo como um conjunto de linhas.

## <a name="automatic-schema-inference"></a>Inferência automática do esquema

Uma vez que os dados são armazenados no formato de ficheiro Parquet, a inferência automática de esquemas está disponível. Pode consultar facilmente os dados sem listar os tipos de dados de todas as colunas nos ficheiros. Também pode utilizar o mecanismo de coluna virtual e a função de filepas para filtrar um determinado subconjunto de ficheiros.

Vamos primeiro familiarizar-nos com os dados do Táxi de NYC, executando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

[O conjunto de dados de táxi da cidade de Nova Iorque (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) inclui:

- Datas e horários de recolha e entrega.
- Locais de recolha e entrega. 
- Distâncias de viagem.
- Tarifas itemizadas.
- Tipos de tarifas.
- Tipos de pagamento. 
- Contagem de passageiros reportada pelo condutor.

Da mesma forma, pode consultar o conjunto de dados dos Feriados Públicos utilizando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Por último, também pode consultar o conjunto de dados de dados meteorológicos utilizando a seguinte consulta:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Pode saber mais sobre o significado das colunas individuais nas descrições dos conjuntos de dados: 
- [Táxi de Nyc](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Dados meteorológicos](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Séries temporências, sazonalidade e análises mais estranhas

Pode resumir facilmente o número anual de passeios de táxi utilizando a seguinte consulta:

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

O seguinte corte mostra o resultado do número anual de passeios de táxi:

![Número anual de passeios de táxi resulta de snippet](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Os dados podem ser visualizados no Synapse Studio mudando da **tabela** para a vista **gráfico.** Pode escolher entre diferentes tipos de gráficos, tais como **Área,** **Bar,** **Coluna,** **Linha,** **Tarte** e **Dispersão.** Neste caso, defina o gráfico **da Coluna** com a coluna **categoria** definida para **current_year**:

![Gráfico de coluna mostrando passeios por ano](./media/tutorial-data-analyst/column-chart-rides-year.png)

A partir desta visualização, pode ver-se uma tendência de diminuição do número de passeios ao longo dos anos. Presumivelmente, este decréscimo deve-se ao recente aumento da popularidade das empresas que partilham boleias.

> [!NOTE]
> No momento da escrita deste tutorial, os dados para 2019 estão incompletos. Como resultado, há uma grande queda no número de passeios para aquele ano.

Em seguida, vamos focar a análise num único ano, por exemplo, 2016. A seguinte consulta devolve o número diário de passeios durante esse ano:

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

O seguinte corte mostra o resultado desta consulta:

![Número diário de passeios para resultados de 2016](./media/tutorial-data-analyst/daily-rides.png)

Mais uma vez, pode visualizar facilmente os dados traçando o gráfico **da Coluna** com a coluna **categoria** definida para **current_day** e a coluna **Legend (série)** definida para **rides_per_day**.

![Gráfico de coluna mostrando número diário de passeios para 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

Do gráfico de enredo, você pode ver que há um padrão semanal, com os sábados como o dia de pico. Durante os meses de verão, há menos passeios de táxi por causa de férias. Além disso, note algumas quedas significativas no número de passeios de táxi sem um padrão claro de quando e por que eles ocorrem.

Em seguida, vamos ver se a queda nos passeios está relacionada com feriados. Podemos ver se há uma correlação juntando o conjunto de dados de passeios de táxi de NYC com o conjunto de dados de Feriados Públicos:

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

![Passeios de táxi e conjuntos de dados de feriados de NYC resultam na visualização](./media/tutorial-data-analyst/rides-public-holidays.png)

Desta vez, queremos destacar o número de passeios de táxi durante os feriados. Para o efeito, **escolhemos nenhuma** para a coluna **categoria** e **rides_per_day** e **feriados** como as colunas **Legend (série).**

![Número de passeios de táxi durante gráfico de enredo de feriados](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

A partir do gráfico de enredo, você pode ver que durante os feriados o número de passeios de táxi é menor. Ainda há uma grande queda inexplicável no dia 23 de janeiro. Vamos verificar o tempo em NYC nesse dia consultando o conjunto de dados meteorológicos:

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

![Visualização do resultado do conjunto de dados meteorológicos](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Os resultados da consulta indicam que a diminuição do número de passeios de táxi ocorreu porque:

- Houve uma tempestade naquele dia em NYC com neve pesada (~30 cm).
- Estava frio (a temperatura estava abaixo de zero graus Celsius).
- Estava ventoso (~10 m/s).

Este tutorial mostrou como um analista de dados pode rapidamente realizar análises exploratórias de dados, combinar facilmente diferentes conjuntos de dados utilizando o pool SQL sem servidor, e visualizar os resultados usando o Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes

Para aprender a ligar a piscina SQL sem servidor ao Power BI Desktop e criar relatórios, consulte [a piscina SQL sem servidor para o Power BI Desktop e crie relatórios](tutorial-connect-power-bi-desktop.md).

Para aprender a usar tabelas externas na piscina SQL sem servidor consulte [Use tabelas externas com Sinapse SQL](develop-tables-external-tables.md?tabs=sql-pool)
 
