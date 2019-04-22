---
title: Exemplos - Hubs de eventos do Azure | Documentos da Microsoft
description: Este artigo fornece uma lista de exemplos para os Hubs de eventos do Azure que estão disponíveis no GitHub.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 1c1198733fb56303d328ee97152442d25dbe945a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682401"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Repositórios de Git com exemplos para os Hubs de eventos do Azure 
Pode encontrar exemplos de Hubs de eventos no [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Estes exemplos demonstram os principais recursos do [os Hubs de eventos do Azure](/azure/event-hubs/). Este artigo categoriza e descreve os exemplos disponíveis, com links para cada um.

## <a name="net-samples"></a>Amostras de .NET

| Nome de exemplo | Descrição | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Este exemplo mostra como escrever uma aplicação de consola .NET Core que envia um conjunto de eventos para um hub de eventos. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Este exemplo mostra como escrever uma aplicação de consola .NET Core que recebe um conjunto de eventos de um hub de eventos com a biblioteca de anfitrião do processador de eventos.  | 

## <a name="java-samples"></a>Exemplos do Java

| Nome de exemplo | Descrição | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Este exemplo ilustra como ingerir lotes de eventos no seu hub de eventos. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Este exemplo ilustra como de ingerir eventos no seu hub de eventos. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Este exemplo ilustra as várias opções disponíveis com os Hubs de eventos para ingerir eventos. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Este exemplo ilustra como receber eventos a partir de uma partição do hub de eventos com um desvio de data e hora específico. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Este exemplo ilustra como receber eventos a partir de uma partição do hub de eventos com um desvio de dados específicos. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Este exemplo ilustra como podem receber de uma partições do hub de eventos utilizando um número de sequência. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Este exemplo ilustra como receber eventos de um hub de eventos com o anfitrião de processador de eventos, que fornece a seleção automática de partição e a ativação pós-falha por vários recetores em simultâneo. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Este exemplo ilustra como um hub de eventos pode automaticamente verticalmente em altas cargas. O exemplo enviará os eventos a uma taxa que apenas excederem a taxa de configurado de um hub de eventos, fazendo com que o hub de eventos aumentar verticalmente. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Este exemplo permite medir a taxa de entrada. | 

## <a name="python-samples"></a>Amostras de Python
Pode encontrar amostras de Python para os Hubs de eventos do Azure no [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) repositório do GitHub.

## <a name="nodejs-samples"></a>Amostras de Node.js
Pode encontrar exemplos de node. js para os Hubs de eventos do Azure no [azure-sdk-para-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) repositório do GitHub.

## <a name="go-samples"></a>Amostras do go
Pode encontrar exemplos de ir para os Hubs de eventos do Azure no [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) repositório do GitHub.

## <a name="azure-cli-samples"></a>Exemplos da CLI do Azure
Pode encontrar exemplos da CLI do Azure para os Hubs de eventos do Azure no [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) repositório do GitHub.

## <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell
Pode encontrar exemplos do Azure PowerShell para os Hubs de eventos do Azure no [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) repositório do GitHub.
 
## <a name="apache-kafka-samples"></a>Exemplos do Apache Kafka
Pode encontrar exemplos para os Hubs de eventos para a funcionalidade do Apache Kafka no [azure-event-hubs-para-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) repositório do GitHub.

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre os Hubs de eventos nos seguintes artigos:

- [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Funcionalidades dos Hubs de Eventos](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)