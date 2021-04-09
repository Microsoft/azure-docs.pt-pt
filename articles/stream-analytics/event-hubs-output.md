---
title: Saída de Hubs de Eventos da Azure Stream Analytics
description: Este artigo descreve como obter dados de produção de Azure Stream Analytics para Azure Event Hubs.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 02abdd752528ce28642b6228648062ed961d5ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452395"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Saída de Hubs de Eventos da Azure Stream Analytics

O serviço [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) é um evento altamente escalável de publicação-subscrição. Pode recolher milhões de eventos por segundo. Um uso de um centro de eventos como saída é quando a saída de um trabalho stream Analytics torna-se a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e a otimização do tamanho do lote, consulte a secção de tamanho do [lote de saída.](#output-batch-size)

## <a name="output-configuration"></a>Configuração de saída

A tabela seguinte tem os parâmetros necessários para configurar fluxos de dados dos centros de eventos como uma saída.

| Nome da propriedade | Description |
| --- | --- |
| Alias de saída | Um nome amigável usado em consultas para direcionar a saída de consulta para este centro de eventos. |
| Espaço de nomes do hub de eventos | Um contentor para um conjunto de entidades de mensagens. Quando criou um novo centro de eventos, também criou um espaço de nomes de centros de eventos. |
| Nome do hub de eventos | O nome da saída do seu centro de eventos. |
| Nome da política do centro de eventos | A política de acesso partilhado, que pode criar no separador **Configure** do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| Chave de política do centro de eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do centro de eventos. |
| Coluna-chave de partição | Opcional. Uma coluna que contém a chave de partição para a saída do centro de eventos. |
| Formato de serialização de eventos | O formato de serialização para dados de saída. JSON, CSV e Avro são apoiados. |
| Encoding | Para cSV e JSON, UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador | Aplicável apenas para serialização de CSV. O Stream Analytics suporta uma série de delimiters comuns para serializar dados em formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON.  |
| Colunas de propriedade | Opcional. Colunas separadas por vírgula que precisam de ser anexadas como propriedades do utilizador da mensagem de saída em vez da carga útil. Mais informações sobre esta funcionalidade estão na secção [Propriedades de metadados personalizados para a saída.](#custom-metadata-properties-for-output) |

## <a name="partitioning"></a>Criação de partições

A partição varia consoante o alinhamento da partição. Quando a chave de partição para a saída do centro de eventos está igualmente alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias na produção do centro de eventos. Cada escritor usa a [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) para enviar eventos para a partição específica. Quando a chave de partição para a saída do centro de eventos não está alinhada com o passo de consulta a montante (anterior), o número de escritores é o mesmo que o número de divisórias nesse passo anterior. Cada escritor usa a [aula SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync) no **EventHubClient** para enviar eventos para todas as divisórias de saída. 

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho máximo da mensagem é de 256 KB ou 1 MB por mensagem. Para mais informações, consulte [os limites do Event Hubs](../event-hubs/event-hubs-quotas.md). Quando a partição de entrada/saída não está alinhada, cada evento é embalado individualmente `EventData` e enviado num lote até ao tamanho máximo da mensagem. Isto também acontece se forem utilizadas [propriedades de metadados personalizados.](#custom-metadata-properties-for-output) Quando a partição de entrada/saída está alinhada, vários eventos são embalados numa única `EventData` instância, até ao tamanho máximo da mensagem, e enviados.

## <a name="custom-metadata-properties-for-output"></a>Propriedades de metadados personalizados para saída

Pode anexar colunas de consulta como propriedades do utilizador às suas mensagens de saída. Estas colunas não entram na carga. As propriedades estão presentes na forma de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados stream Analytics são suportados, exceto Record e Array.

No exemplo seguinte, os campos `DeviceId` são `DeviceStatus` adicionados aos metadados.

1. Utilize a seguinte consulta:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configurar `DeviceId,DeviceStatus` como colunas de propriedade na saída.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Colunas de propriedade":::

A imagem a seguir é das propriedades de mensagens de saída esperadas inspecionadas no EventHub utilizando [o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Propriedades personalizadas do evento":::

## <a name="next-steps"></a>Passos seguintes

* [Utilize identidades geridas para aceder ao Event Hub a partir de um trabalho de Azure Stream Analytics (Preview)](event-hubs-managed-identity.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
