---
title: 'Receita: Manutenção preditiva com os Serviços Cognitivos para Big Data'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como executar a deteção de anomalias distribuídas com os Serviços Cognitivos para Big Data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: aa650fe9cb7df64a6a7a948224be225ecfad9057
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324607"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Receita: Manutenção preditiva com os Serviços Cognitivos para Big Data

Esta receita mostra como você pode usar Azure Synapse Analytics e Serviços Cognitivos em Apache Spark para manutenção preditiva de dispositivos IoT. Seguiremos junto com a amostra [cosmosDb e Synapse Link.](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) Para manter as coisas simples, nesta receita vamos ler os dados diretamente de um ficheiro CSV em vez de obter dados transmitidos através do CosmosDB e Synapse Link. Encorajamo-lo a olhar para a amostra da Synapse Link.

## <a name="hypothetical-scenario"></a>Cenário hipotético

O cenário hipotético é uma Central Elétrica, onde os dispositivos IoT estão a monitorizar [as turbinas](https://en.wikipedia.org/wiki/Steam_turbine)a vapor. A coleção IoTSignals tem dados de Revoluções por minuto (RPM) e Megawatts (MW) para cada turbina. Estão a ser analisados sinais de turbinas a vapor e detetados sinais anómalos.

Pode haver fora dos dados numa frequência aleatória. Nestas situações, os valores de RPM subirão e a saída de MW diminuirá, para proteção de circuitos. A ideia é ver os dados a variar ao mesmo tempo, mas com sinais diferentes.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Espaço de trabalho Azure Synapse](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) configurado com uma [piscina Apache Spark sem servidor](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool)

## <a name="setup"></a>Configuração

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para Tradutor utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [O Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli). Também pode:

- Ver um recurso existente no  [portal Azure](https://portal.azure.com/).

Tome nota do ponto final e da chave para este recurso, você vai precisar neste guia.

## <a name="enter-your-service-keys"></a>Insira as suas chaves de serviço

Vamos começar por adicionar a sua chave e localização.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Ler dados num DataFrame

Em seguida, vamos ler o ficheiro IoTSignals num DataFrame. Abra um novo caderno no seu espaço de trabalho Synapse e crie um DataFrame a partir do ficheiro.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Executar deteção de anomalias usando serviços cognitivos em faísca

O objetivo é encontrar casos em que os sinais dos dispositivos IoT estavam a sair valores anómalos para que possamos ver quando algo está a correr mal e fazer manutenção preditiva. Para isso, vamos usar o Detetor de Anomalias na Faísca:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Vamos ver os dados:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Esta célula deve dar um resultado que se pareça com:

| carimbo de data/hora           |   valor | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Falso       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Falso       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Falso       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Visualizar anomalias para um dos dispositivos

IoTSignals.csv tem sinais de vários dispositivos IoT. Vamos focar-nos num dispositivo específico e visualizar saídas anómalas do dispositivo.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Agora que criámos um dataframe que representa as anomalias para um determinado dispositivo, podemos visualizar estas anomalias:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Se for bem sucedido, a sua saída será assim:

![Enredo do detetor de anomalias](../media/anomaly-output.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a fazer manutenção preditiva em escala com a Azure Cognitive Services, Azure Synapse Analytics e Azure CosmosDB. Para mais informações, consulte a amostra completa no [GitHub.](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)
