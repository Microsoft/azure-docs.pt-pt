---
title: Entender o roteamento de mensagens do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-como usar o roteamento de mensagens para enviar mensagens do dispositivo para a nuvem. Inclui informações sobre como enviar dados de telemetria e não telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d2d4d39cc7b330794094745851856365ef54b42f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828198"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O roteamento de mensagens permite que você envie mensagens de seus dispositivos para os serviços de nuvem de maneira automatizada, escalonável e confiável. O roteamento de mensagens pode ser usado para: 

* **Enviar mensagens de telemetria do dispositivo, bem como eventos** , eventos de ciclo de vida do dispositivo e eventos de alteração de troca de dispositivo para os pontos de extremidade internos e personalizados. Saiba mais sobre [pontos de extremidade de roteamento](#routing-endpoints).

* **Filtragem de dados antes de roteá-los para vários pontos de extremidade** aplicando consultas avançadas. O roteamento de mensagens permite consultar as propriedades da mensagem e o corpo da mensagem, bem como as marcas de dispositivo e as propriedades do dispositivo. Saiba mais sobre como usar [consultas no roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade de serviço para que o roteamento de mensagens funcione. Se você configurar seus pontos de extremidade por meio do portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar seus serviços para dar suporte à taxa de transferência esperada. Ao configurar pela primeira vez sua solução de IoT, talvez seja necessário monitorar seus pontos de extremidade adicionais e fazer os ajustes necessários para a carga real.

O Hub IoT define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens do dispositivo para a nuvem para interoperabilidade entre protocolos. Se uma mensagem corresponder a várias rotas que apontam para o mesmo ponto de extremidade, o Hub IoT entregará a mensagem para esse ponto de extremidade apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação em sua fila ou tópico do barramento de serviço. Em filas particionadas, a afinidade de partição garante a ordenação de mensagens. Use este tutorial para aprender a [Configurar o roteamento de mensagens](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos de extremidade de roteamento

Um hub IoT tem um ponto de extremidade interno padrão (**mensagens/eventos**) que é compatível com os hubs de eventos. Você pode criar [pontos de extremidade personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para rotear mensagens para o vinculando outros serviços em sua assinatura ao Hub IOT. O Hub IoT atualmente dá suporte aos seguintes serviços como pontos de extremidade personalizados:

### <a name="built-in-endpoint"></a>Ponto de extremidade interno

Você pode usar a [integração de hubs de eventos padrão e SDKs](iot-hub-devguide-messages-read-builtin.md) para receber mensagens do dispositivo para a nuvem do ponto de extremidade interno (**mensagens/eventos**). Depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Hub IoT dá suporte à gravação de dados no armazenamento de BLOBs do Azure no formato [Apache Avro](https://avro.apache.org/) , bem como no formato JSON. A capacidade de codificar o formato JSON está geralmente disponível em todas as regiões em que o Hub IoT está disponível. O padrão é AVRO. O formato de codificação só pode ser definido quando o ponto de extremidade do armazenamento de BLOBs é configurado. O formato não pode ser editado para um ponto de extremidade existente. Ao usar a codificação JSON, você deve definir o contentType como JSON e contentEncoding como UTF-8 nas [Propriedades do sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Se isso não estiver definido, o Hub IoT gravará as mensagens no formato codificado 64 base. Você pode selecionar o formato de codificação usando o Hub IoT criar ou atualizar a API REST, especificamente o [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), o portal do Azure, o [CLI do Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)ou o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). O diagrama a seguir mostra como selecionar o formato de codificação no portal do Azure.

![Codificação de ponto de extremidade de armazenamento de BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

O Hub IoT também dá suporte a mensagens de roteamento para ADLS Gen2 contas, que são contas de armazenamento hierárquicas habilitadas [para namespaces](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)criadas com base no armazenamento de BLOBs. Essa funcionalidade está em visualização pública e disponível para novas contas de ADLS Gen2 no oeste dos EUA 2 e no Oeste EUA Central. Em breve, iremos distribuir esse recurso para todas as regiões de nuvem.

O Hub IoT faz o lote de mensagens e grava dados em um blob sempre que o lote atinge um determinado tamanho ou um determinado período de tempo decorrido. O Hub IoT usa como padrão a seguinte convenção de nomenclatura de arquivo:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Você pode usar qualquer Convenção de nomenclatura de arquivo, no entanto, deve usar todos os tokens listados. O Hub IoT será gravado em um blob Vazio se não houver dados a serem gravados.

Ao rotear para o armazenamento de BLOBs, é recomendável inlistar os BLOBs e, em seguida, iterar sobre eles, para garantir que todos os contêineres sejam lidos sem fazer nenhuma suposição de partição. O intervalo de partição pode ser alterado durante um failover [manual](iot-hub-ha-dr.md#manual-failover-preview)ou de [failover iniciado pela Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) . Você pode usar a [API listar BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para enumerar a lista de BLOBs. Consulte o exemplo a seguir como orientação.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Tópicos de filas do barramento de serviço e barramento de serviço

As filas e os tópicos do barramento de serviço usados como pontos de extremidade do Hub IoT não devem ter **sessões** ou **detecção** de duplicidades habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como inacessível no portal do Azure.

### <a name="event-hubs"></a>Hubs de Eventos

Além do ponto de extremidade compatível com os hubs de eventos internos, você também pode rotear dados para pontos de extremidade personalizados do tipo hubs de eventos. 

## <a name="reading-data-that-has-been-routed"></a>Lendo dados que foram roteados

Você pode configurar uma rota seguindo este [tutorial](tutorial-routing.md).

Use os tutoriais a seguir para saber como ler a mensagem de um ponto de extremidade.

* Lendo do [ponto de extremidade interno](quickstart-send-telemetry-node.md)

* Lendo do [armazenamento](../storage/blobs/storage-blob-event-quickstart.md) de BLOBs

* Lendo dos [hubs de eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Leitura de [filas do barramento de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Leia os [Tópicos do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Rota de fallback

A rota de fallback envia todas as mensagens que não satisfazem as condições de consulta em nenhuma das rotas existentes para os hubs de eventos internos (**mensagens/eventos**) que são compatíveis com os [hubs de eventos](/azure/event-hubs/). Se o roteamento de mensagens estiver ativado, você poderá habilitar o recurso de rota de fallback. Depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade. Se não houver nenhuma rota para o ponto de extremidade interno e uma rota de fallback estiver habilitada, somente as mensagens que não corresponderem a nenhuma condição de consulta nas rotas serão enviadas para o ponto de extremidade interno. Além disso, se todas as rotas existentes forem excluídas, a rota de fallback deverá ser habilitada para receber todos os dados no ponto de extremidade interno.

Você pode habilitar/desabilitar a rota de fallback na folha de roteamento de mensagens portal do Azure >. Você também pode usar Azure Resource Manager para [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) usar um ponto de extremidade personalizado para a rota de fallback.

## <a name="non-telemetry-events"></a>Eventos de não telemetria

Além da telemetria do dispositivo, o roteamento de mensagens também permite enviar eventos de alteração de dispositivo e eventos de ciclo de vida do dispositivo. Por exemplo, se uma rota for criada com a fonte de dados definida como **eventos de alteração de dispositivos de troca**, o Hub IOT enviará mensagens para o ponto de extremidade que contêm a alteração no dispositivo. Da mesma forma, se uma rota for criada com a fonte de dados definida como **eventos de ciclo de vida do dispositivo**, o Hub IOT enviará uma mensagem indicando se o dispositivo foi excluído ou criado. 

O [Hub IOT também se integra à grade de eventos do Azure](iot-hub-event-grid.md) para publicar eventos de dispositivo para dar suporte a integrações em tempo real e automação de fluxos de trabalho com base nesses eventos. Consulte as principais [diferenças entre roteamento de mensagens e a grade de eventos](iot-hub-event-grid-routing-comparison.md) para saber qual funciona melhor para seu cenário.

## <a name="testing-routes"></a>Testando rotas

Quando você cria uma nova rota ou edita uma rota existente, deve testar a consulta de rota com uma mensagem de exemplo. Você pode testar rotas individuais ou testar todas as rotas de uma vez e nenhuma mensagem é roteada para os pontos de extremidade durante o teste. Portal do Azure, Azure Resource Manager, Azure PowerShell e CLI do Azure podem ser usados para teste. Os resultados ajudam a identificar se a mensagem de exemplo correspondeu à consulta, se a mensagem não correspondeu à consulta ou não foi possível executar o teste porque a sintaxe da mensagem de exemplo ou da consulta está incorreta. Para saber mais, consulte [testar rota](/rest/api/iothub/iothubresource/testroute) e [testar todas as rotas](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latência

Quando você roteia mensagens de telemetria do dispositivo para a nuvem usando pontos de extremidade internos, há um pequeno aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio na latência é menor que 500 ms. Você pode monitorar a latência usando **Roteamento: latência de mensagem para mensagens/eventos** ou **D2C. endpoints. latência. builtIn. eventos** métrica do Hub IOT. Criar ou excluir qualquer rota depois da primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

O Hub IoT fornece várias métricas relacionadas ao roteamento e aos pontos de extremidade para fornecer uma visão geral da integridade do seu hub e das mensagens enviadas. Você pode combinar informações de várias métricas para identificar a causa raiz de problemas. Por exemplo, use o **Roteamento de métrica: mensagens** de telemetria removidas ou **D2C. telemetria. egresso.** descartada para identificar o número de mensagens que foram descartadas quando não corresponderam a consultas em qualquer uma das rotas e a rota de fallback foi desabilitada. As métricas do [Hub IOT](iot-hub-metrics.md) lista todas as métricas habilitadas por padrão para o Hub IOT.

Você pode usar a integridade do [ponto de extremidade Get](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o [status de integridade](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos de extremidade. É recomendável usar as métricas do [Hub IOT](iot-hub-metrics.md) relacionadas à latência da mensagem de roteamento para identificar e depurar erros quando a integridade do ponto de extremidade está inativa ou não íntegra. Por exemplo, para hubs de eventos de tipo de ponto de extremidade, você pode monitorar **D2C. endpoints. latência. eventHubs**. O status de um ponto de extremidade não íntegro será atualizado para íntegro quando o Hub IoT tiver estabelecido um estado de integridade eventualmente consistente.

Usando os logs de diagnóstico de **rotas** em Azure monitor [configurações de diagnóstico](../iot-hub/iot-hub-monitor-resource-health.md), você pode rastrear os erros que ocorrem durante a avaliação de uma consulta de roteamento e integridade do ponto de extremidade, conforme percebido pelo Hub IOT, por exemplo, quando um ponto de extremidade está inativo. Esses logs de diagnóstico podem ser enviados para Azure Monitor logs, hubs de eventos ou armazenamento do Azure para processamento personalizado.

## <a name="next-steps"></a>Passos Seguintes

* Para saber como criar rotas de mensagens, consulte [processar mensagens do dispositivo para a nuvem do Hub IOT usando rotas](tutorial-routing.md).

* [Como enviar mensagens do dispositivo para a nuvem](quickstart-send-telemetry-node.md)

* Para obter informações sobre os SDKs que você pode usar para enviar mensagens do dispositivo para a nuvem, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).
