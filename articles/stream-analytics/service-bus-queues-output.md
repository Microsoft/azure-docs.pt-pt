---
title: Saída de filas de autocarros de serviço a partir de Azure Stream Analytics
description: Este artigo descreve as filas do Service Bus como saída para o Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 9f71a0f9c6d3fdca4b81be3a69479c78236fcc31
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014217"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Saída de filas de autocarros de serviço a partir de Azure Stream Analytics

[As filas de ônibus de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferecem uma entrega de mensagens FIFO a um ou mais consumidores concorrentes. Normalmente, as mensagens são recebidas e processadas pelos recetores na ordem temporal em que foram adicionadas à fila. Cada mensagem é recebida e processada por apenas um consumidor de mensagens.

No [nível de compatibilidade 1.2](stream-analytics-compatibility-level.md), o Azure Stream Analytics utiliza o protocolo de mensagens [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) para escrever para Service Bus Queues and Topics. A AMQP permite-lhe construir aplicações híbridas e inter-plataforma usando um protocolo padrão aberto.

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação de uma saída de fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta fila de serviço. |
| Espaço de nome de ônibus de serviço |Um contentor para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do autocarro de serviço. |
| Nome da política da fila |Quando cria uma fila, também pode criar políticas de acesso partilhado no **separador Configurar** da fila. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave de política de fila |A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nomes do Service Bus. |
| Formato de serialização de eventos |O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding |Para cSV e JSON, UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador |Aplicável apenas para serialização de CSV. O Stream Analytics suporta uma série de delimiters comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato |Aplicável apenas para o tipo JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. |
| Colunas de propriedade | Opcional. Colunas separadas por vírgula que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para a saída.](#custom-metadata-properties-for-output) |
| Colunas de propriedade do sistema | Opcional. Os pares de valor chave das Propriedades do Sistema e os nomes correspondentes das colunas que precisam de ser anexados à mensagem de saída em vez da carga útil.  |

O número de divisórias [baseia-se no Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição.

## <a name="partitioning"></a>Criação de partições

A partilha é escolhida automaticamente. O número de divisórias baseia-se no [Serviço de Autocarros SKU e tamanho.](../service-bus-messaging/service-bus-partitioning.md) A chave de partição é um valor inteiro único para cada partição. O número de escritores de saída é o mesmo que o número de divisórias na fila de saída.

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho máximo da mensagem é de 256 KB por mensagem para o nível Standard e 1MB para o nível Premium. Para mais informações, consulte [os limites do Service Bus.](../service-bus-messaging/service-bus-quotas.md) Para otimizar, utilize um único evento por mensagem.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizados para saída

Pode anexar colunas de consulta como propriedades do utilizador às suas mensagens de saída. Estas colunas não entram na carga. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados stream Analytics são suportados, exceto Record e Array.

No exemplo seguinte, os campos `DeviceId` são `DeviceStatus` adicionados aos metadados.

1. Utilize a seguinte consulta:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configurar `DeviceId,DeviceStatus` como colunas de propriedade na saída.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Colunas de propriedade":::

A imagem a seguir é das propriedades de mensagens de saída esperadas inspecionadas no EventHub utilizando [o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Propriedades personalizadas do evento":::

## <a name="system-properties"></a>Propriedades do sistema

Pode anexar colunas de consulta como [propriedades do sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) à fila do autocarro de serviço de saída ou mensagens tópicos.

Estas colunas não entram na carga útil, em vez disso, a propriedade correspondente do [sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) BrokeredMessage é povoada com os valores da coluna de consulta.
Estas propriedades do sistema são suportadas - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Os valores de cadeia destas colunas são analisados como o tipo de valor contabilístico correspondente do sistema e quaisquer falhas de análise são tratadas como erros de dados.
Este campo é fornecido como um formato de objeto JSON. Os detalhes sobre este formato são os seguintes:

* Rodeada por aparelhos {} encaracolados.
* Escrito em pares chave/valor.
* As chaves e os valores devem ser cordas.
* Chave é o nome e valor da propriedade do sistema é o nome da coluna de consulta.
* As chaves e os valores são separados por um cólon.
* Cada par de chaves/valor é separado por uma vírgula.

Isto mostra como usar esta propriedade –

* Consulta: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas de Propriedade do Sistema: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Isto define as mensagens de fila de `MessageId` autocarros de serviço com `column1` os valores e partitionKey é definido com `column2` os valores de 's'

## <a name="next-steps"></a>Próximos passos

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)