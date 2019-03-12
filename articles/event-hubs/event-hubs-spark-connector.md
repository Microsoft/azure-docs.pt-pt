---
title: Integrar com o Apache Spark - Hubs de eventos do Azure | Documentos da Microsoft
description: Integrar com o Apache Spark para ativar a transmissão em fluxo estruturada com os Hubs de eventos
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 605669a740663040ab7a167bf266fe1940123afc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551879"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integração do Apache Spark com os Hubs de eventos do Azure

Os Hubs de eventos do Azure se integra perfeitamente [Apache Spark](https://spark.apache.org/) para ativar modulares distribuídas aplicações de transmissão em fluxo. Esta integração suporta [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html), e [transmissão em fluxo estruturada](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). O conector de Hubs de eventos para o Apache Spark está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-spark). Esta biblioteca também está disponível para uso em projetos Maven a partir da [repositório Central Maven](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Este artigo descreve como criar um aplicativo contínuo no [Azure Databricks](https://azure.microsoft.com/services/databricks/). Embora este artigo utiliza o Azure Databricks, os clusters do Spark também estão disponíveis com [HDInsight](../hdinsight/spark/apache-spark-overview.md).

O exemplo neste artigo utiliza dois blocos de notas Scala: um para transmissão em fluxo de eventos a partir de um hub de eventos e outro para o envio de eventos para ele.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver um, [criar uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Uma instância de Hubs de eventos. Se não tiver um, [criá-lo](event-hubs-create.md).
* Uma [Azure Databricks](https://azure.microsoft.com/services/databricks/) instância. Se não tiver um, [criá-lo](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Criar uma biblioteca com as coordenadas do maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Eventos de Stream do seu hub de eventos usando o seguinte código:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
O seguinte código para enviar eventos para o hub de eventos com o APIs de batch do Spark. Também pode escrever uma consulta de transmissão em fluxo para enviar eventos para o hub de eventos:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como configurar um fluxo dimensionável, tolerante a falhas utilizando o conector de Hubs de eventos para o Apache Spark. Saiba mais sobre como utilizar os Hubs de eventos com transmissão em fluxo estruturada e Spark Streaming seguindo estes links:

* [Transmissão em fluxo estruturada + guia de integração do Hubs de eventos do Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Transmissão em fluxo do Spark + guia de integração do Hubs de eventos](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
