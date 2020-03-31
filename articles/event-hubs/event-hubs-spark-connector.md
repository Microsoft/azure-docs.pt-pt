---
title: Integrar com Apache Spark - Hubs de eventos Azure [ Microsoft Docs
description: Este artigo mostra como se integrar com a Apache Spark para permitir o Streaming Estruturado com Hubs de Eventos.
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
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311687"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrando a Faísca Apache com hubs de eventos azure

O Azure Event Hubs integra-se perfeitamente com [a Apache Spark](https://spark.apache.org/) para permitir a construção de aplicações de streaming distribuídas. Esta integração suporta [o Spark Core,](https://spark.apache.org/docs/latest/rdd-programming-guide.html) [o Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)e o Streaming [Estruturado.](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) O conector De Eventos Hubs para Apache Spark está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-spark). Esta biblioteca também está disponível para uso em projetos Maven do [Repositório Central maven.](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

Este artigo descreve como criar uma aplicação contínua em [Tijolos de Dados Azure.](https://azure.microsoft.com/services/databricks/) Enquanto este artigo utiliza os Bricks Azure, os clusters Spark também estão disponíveis com [o HDInsight](../hdinsight/spark/apache-spark-overview.md).

O exemplo deste artigo usa dois cadernos Scala: um para streaming de eventos a partir de um centro de eventos e outro para enviar eventos de volta para ele.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um caso de Hubs de Eventos. Se não tiver um, [crie um.](event-hubs-create.md)
* Uma instância [azure databricks.](https://azure.microsoft.com/services/databricks/) Se não tiver um, [crie um.](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Criar uma biblioteca utilizando coordenadas maven:](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Transmita eventos a partir do seu centro de eventos usando o seguinte código:

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
O código seguinte envia eventos para o seu centro de eventos com as APIs do lote spark. Também pode escrever uma consulta de streaming para enviar eventos para o centro do evento:

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

## <a name="next-steps"></a>Passos seguintes

Agora sabe como configurar um fluxo escalável e tolerante a falhas usando o Conector De Hubs de Eventos para Apache Spark. Saiba mais sobre a utilização de Hubs de Eventos com Streaming Estruturado e Streaming de Faíscas seguindo estes links:

* [Guia de Integração de Centros de Streaming Estruturado + Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guia de integração de centros de eventos + eventos](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
