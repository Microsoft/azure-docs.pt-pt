---
title: Compreenda o encaminhamento de mensagens Azure IoT Hub | Microsoft Docs
description: Guia do desenvolvedor - como utilizar o encaminhamento de mensagens para enviar mensagens dispositivo-a-nuvem. Inclui informações sobre o envio de dados de telemetria e não telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: d2fe8f32ec854e1e6db644a039e6a249cfbddcaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012892"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Utilize o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O encaminhamento de mensagens permite-lhe enviar mensagens dos seus dispositivos para serviços na nuvem de forma automatizada, escalável e fiável. O encaminhamento de mensagens pode ser utilizado para: 

* **Envio de mensagens de telemetria do dispositivo, bem como eventos,** eventos de ciclo de vida do dispositivo, eventos de mudança de dispositivos e eventos de mudança de gémeos digitais para os pontos finais incorporados e personalizados. Saiba mais [sobre os pontos finais de encaminhamento](#routing-endpoints). Para saber mais sobre os eventos enviados a partir de dispositivos IoT Plug e Play, consulte [o Understand IoT Plug e play digital twins](../iot-pnp/concepts-digital-twin.md).

* **Filtrar dados antes de encaminhá-lo para vários pontos finais** aplicando consultas ricas. O encaminhamento de mensagens permite-lhe consultar as propriedades da mensagem e o corpo da mensagem, bem como as etiquetas gémeas do dispositivo e as propriedades gémeas do dispositivo. Saiba mais sobre a utilização [de consultas no encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O IoT Hub precisa de escrever acesso a estes pontos finais de serviço para o encaminhamento de mensagens para funcionar. Se configurar os seus pontos finais através do portal Azure, as permissões necessárias são adicionadas para si. Certifique-se de que configura os seus serviços para suportar a produção esperada. Por exemplo, se estiver a utilizar o Event Hubs como um ponto final personalizado, tem de configurar as **unidades de produção** para esse centro de eventos para que possa lidar com a entrada de eventos que planeia enviar através do encaminhamento de mensagens IoT Hub. Da mesma forma, ao utilizar uma fila de autocarros de serviço como ponto final, deve configurar o **tamanho máximo** para garantir que a fila pode reter todos os dados ingressados, até que seja escrulida pelos consumidores. Quando configurar pela primeira vez a sua solução IoT, poderá ter de monitorizar os seus pontos finais adicionais e fazer os ajustes necessários para a carga real.

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens dispositivo-a-nuvem para interoperabilidade através de protocolos. Se uma mensagem corresponder a várias rotas que apontam para o mesmo ponto final, o IoT Hub entrega a mensagem a esse ponto final apenas uma vez. Por isso, não precisa de configurar a deduplicação na sua fila ou tópico do Service Bus. Nas filas divididas, a afinidade da partição garante a ordem de mensagem. Utilize este tutorial para aprender a configurar o [encaminhamento de mensagens](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos finais de encaminhamento

Um hub IoT tem um ponto final incorporado padrão **(mensagens/eventos)** que é compatível com Os Centros de Eventos. Pode criar [pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para encaminhar mensagens para, ligando outros serviços na sua subscrição ao IoT Hub. 

Cada mensagem é encaminhada para todos os pontos finais cujas consultas de encaminhamento correspondem. Por outras palavras, uma mensagem pode ser encaminhada para vários pontos finais.

Se o seu ponto final personalizado tiver configurações de firewall, considere usar a microsoft fidedigna primeira exceção de primeira parte, para dar ao seu IoT Hub acesso ao ponto final específico - [Azure Storage,](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) [Azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) e [Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Isto está disponível em regiões selecionadas para IoT Hubs com [identidade de serviço gerida.](./virtual-network-support.md)

O IoT Hub suporta atualmente os seguintes pontos finais:

 - Ponto final incorporado
 - Armazenamento do Azure
 - Filas de autocarros de serviço e tópicos de ônibus de serviço
 - Hubs de Eventos

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Ponto final incorporado como ponto final de encaminhamento

Pode utilizar a integração padrão [dos Centros de Eventos e os SDKs](iot-hub-devguide-messages-read-builtin.md) para receber mensagens dispositivo-a-nuvem a partir do ponto final incorporado **(mensagens/eventos).** Uma vez criada uma Rota, os dados param de fluir para o ponto final incorporado, a menos que seja criada uma Rota para esse ponto final.

## <a name="azure-storage-as-a-routing-endpoint"></a>Armazenamento Azure como ponto final de encaminhamento

Existem dois serviços de armazenamento IoT Hub que podem encaminhar mensagens para -- [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). As contas de armazenamento do Lago de Dados Azure são contas de armazenamento [hierárquicas](../storage/blobs/data-lake-storage-namespace.md)ativadas pelo espaço de identificação construídas em cima do armazenamento de bolhas. Ambos usam bolhas para o seu armazenamento.

O IoT Hub suporta a escrita de dados para o Azure Storage no formato [Apache Avro,](https://avro.apache.org/) bem como no formato JSON. O padrão é AVRO. Ao utilizar a codificação JSON, deve definir o conteúdoType para **aplicação/json** e conteúdoEncoding para **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagem . Ambos os valores são insensíveis a caso. Se a codificação de conteúdo não estiver definida, o IoT Hub escreverá as mensagens no formato codificado base 64.

O formato de codificação só pode ser definido quando o ponto final de armazenamento do blob estiver configurado; não pode ser editado para um ponto final existente. Para mudar os formatos de codificação para um ponto final existente, terá de eliminar e recriar o ponto final personalizado com o formato que pretende. Uma estratégia útil pode ser criar um novo ponto final personalizado com o seu formato de codificação desejado e adicionar uma rota paralela a esse ponto final. Desta forma, pode verificar os seus dados antes de eliminar o ponto final existente.

Pode selecionar o formato de codificação utilizando o IoT Hub Create ou Atualizar a API REST, especificamente o [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), o portal [Azure CLI,](/cli/azure/iot/hub/routing-endpoint)ou o [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). A imagem a seguir mostra como selecionar o formato de codificação no portal Azure.

![Codificação do ponto final de armazenamento de bolhas](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

O IoT Hub emoto de mensagens e escreve dados para armazenamento sempre que o lote atinge um determinado tamanho ou uma certa quantidade de tempo decorrido. IoT Hub desresusa a seguinte convenção de nomeação de ficheiros:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Pode utilizar qualquer convenção de nomeação de ficheiros, no entanto deve utilizar todos os tokens listados. O IoT Hub escreverá a uma bolha vazia se não houver dados para escrever.

Recomendamos que se listem as bolhas ou ficheiros e, em seguida, iterando sobre eles, para garantir que todas as bolhas ou ficheiros são lidos sem fazer quaisquer suposições de partição. A gama de divisórias pode potencialmente mudar durante uma falha de [failover](iot-hub-ha-dr.md#microsoft-initiated-failover) iniciada pela Microsoft ou [falha manual](iot-hub-ha-dr.md#manual-failover)do IoT Hub . Pode utilizar a [Lista Blobs API](/rest/api/storageservices/list-blobs) para enumerar a lista de blobs ou [Lista ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/list) para a lista de ficheiros. Por favor, consulte a seguinte amostra como orientação.

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

Para criar uma conta de armazenamento compatível com a Azure Data Lake Gen2, crie uma nova conta de armazenamento V2 e selecione *ativada* no campo *de espaço de nome hierárquico* no separador **Avançado,** como mostra a seguinte imagem:

![Selecione Azure Date Lake Gen2 armazenamento](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Filas de autocarros de serviço e tópicos de ônibus de serviço como um ponto final de encaminhamento

As filas e tópicos do Service Bus utilizados como pontos finais do IoT Hub não devem ter **sessões** ou **deteção duplicada** ativadas. Se uma dessas opções estiver ativada, o ponto final aparece como **Inalcançável** no portal Azure.

## <a name="event-hubs-as-a-routing-endpoint"></a>Hubs de evento como um ponto final de encaminhamento

Além do ponto final compatível com os Hubs de Eventos incorporados, também pode encaminhar dados para pontos finais personalizados do tipo Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Ler dados que foram encaminhados

Pode configurar um percurso seguindo este [tutorial.](tutorial-routing.md)

Use os seguintes tutoriais para aprender a ler a mensagem a partir de um ponto final.

* Leitura de [Built-in-endpoint](quickstart-send-telemetry-node.md)

* Leitura do [armazenamento blob](../storage/blobs/storage-blob-event-quickstart.md)

* Leitura de Centros de [Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Leitura das filas de [autocarros de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Leia a partir de tópicos de [ônibus de serviço](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Rota do recuo

A rota de retorno envia todas as mensagens que não satisfazem as condições de consulta em qualquer uma das rotas existentes para os Hubs incorporados em Eventos **(mensagens/eventos),** que são compatíveis com os Centros de [Eventos.](../event-hubs/index.yml) Se o encaminhamento de mensagens estiver ligado, pode ativar a capacidade da rota de recuo. Uma vez criada uma rota, os dados param de fluir para o ponto final incorporado, a menos que seja criada uma rota para esse ponto final. Se não houver rotas para o ponto de entrada embutido e uma rota de retorno estiver ativada, apenas mensagens que não correspondam a quaisquer condições de consulta nas rotas serão enviadas para o ponto de finalidade incorporado. Além disso, se todas as rotas existentes forem eliminadas, a rota de retorno deve ser ativada para receber todos os dados no ponto de extremidade incorporado.

Pode ativar/desativar a rota de recuo na lâmina de encaminhamento de mensagens de >portal Azure. Também pode utilizar o Azure Resource Manager para [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para utilizar um ponto final personalizado para a rota de retorno.

## <a name="non-telemetry-events"></a>Eventos de não telemetria

Além da telemetria do dispositivo, o encaminhamento de mensagens também permite o envio de eventos de mudança de dois dispositivos, eventos de ciclo de vida do dispositivo e eventos de mudança de gémeos digitais. Por exemplo, se uma rota for criada com fonte de dados definida para **eventos de mudança dupla** do dispositivo, o IoT Hub envia mensagens para o ponto final que contêm a alteração no twin do dispositivo. Da mesma forma, se uma rota for criada com a origem de dados definida para **eventos de ciclo de vida** do dispositivo, o IoT Hub envia uma mensagem indicando se o dispositivo foi eliminado ou criado. Finalmente, como parte do [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md), um desenvolvedor pode criar rotas com fonte de **dados definidas para eventos de mudança de gémeos digitais** e o IoT Hub envia mensagens sempre que uma propriedade digital twin é definida ou alterada, um twin digital é substituído, ou quando um evento de mudança acontece para o twin dispositivo subjacente.

[O IoT Hub também se integra com a Azure Event Grid](iot-hub-event-grid.md) para publicar eventos de dispositivos para apoiar integrações em tempo real e automatização de fluxos de trabalho baseados nestes eventos. Consulte as principais [diferenças entre o encaminhamento de mensagens e a Grade de Eventos](iot-hub-event-grid-routing-comparison.md) para saber qual o melhor funciona para o seu cenário.

## <a name="testing-routes"></a>Rotas de teste

Quando criar uma nova rota ou editar uma rota existente, deverá testar a consulta de rota com uma mensagem de amostra. Pode testar rotas individuais ou testar todas as rotas de uma só vez e nenhuma mensagem é encaminhada para os pontos finais durante o teste. O portal Azure, O Gestor de Recursos Azure, a Azure PowerShell e o Azure CLI podem ser utilizados para testes. Os resultados ajudam a identificar se a mensagem da amostra corresponde à consulta, a mensagem não corresponde à consulta, ou o teste não pôde ser executado porque a mensagem de amostra ou a sintaxe de consulta estão incorretas. Para saber mais, consulte [a Rota de Teste](/rest/api/iothub/iothubresource/testroute) e Teste todas as [rotas.](/rest/api/iothub/iothubresource/testallroutes)

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Encomendando garantias com pelo menos uma vez entrega

O encaminhamento de mensagens IoT Hub garante o pedido e, pelo menos, uma vez entregue mensagens nos pontos finais. Isto significa que pode haver mensagens duplicadas e uma série de mensagens podem ser retransmitidas honrando a ordem original da mensagem. Por exemplo, se a ordem original de mensagem for [1,2,3,4], poderá receber uma sequência de mensagem como [1,2,1,2,3,1,2,3,4]. A garantia de encomenda é que se alguma vez receber mensagem [1], ela seria sempre seguida por [2,3,4].

Para o manuseamento de duplicados de mensagens, recomendamos a estampagem de um identificador único nas propriedades da aplicação da mensagem no ponto de origem, que normalmente é um dispositivo ou um módulo. O serviço que consome as mensagens pode lidar com mensagens duplicadas utilizando este identificador.

## <a name="latency"></a>Latência

Quando se encaminha mensagens de telemetria de dispositivo para nuvem utilizando pontos finais incorporados, há um ligeiro aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio da latência é inferior a 500 ms. Pode monitorizar a latência utilizando o **encaminhamento: latência da mensagem para mensagens/eventos** ou **d2c.endpoints.latncy.builtIn.events** IoT Hub metric. Criar ou excluir qualquer rota após a primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitorizar e resolver problemas

O IoT Hub fornece várias métricas relacionadas com o encaminhamento e pontos finais para lhe dar uma visão geral da saúde do seu hub e mensagens enviadas. Para obter uma lista de todas as métricas do IoT Hub, divididas por categoria funcional, consulte [métricas na referência de dados de monitorização](monitor-iot-hub-reference.md#metrics). Pode rastrear erros que ocorram durante a avaliação de uma consulta de encaminhamento e saúde de ponto final como percebido pelo IoT Hub com a categoria [ **de rotas** em registos de recursos IoT Hub](monitor-iot-hub-reference.md#routes). Para saber mais sobre a utilização de métricas e registos de recursos com o IoT Hub, consulte [o Monitor IoT Hub](monitor-iot-hub.md).

Pode utilizar a API [Rest Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de [saúde](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos finais.

Utilize o [guia de resolução de problemas para encaminhamento](troubleshoot-message-routing.md) para obter mais detalhes e suporte para o encaminhamento de resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar rotas de mensagens, consulte [mensagens de dispositivo ioT hub do processo para nuvem utilizando rotas.](tutorial-routing.md)

* [Como enviar mensagens dispositivo-a-nuvem](quickstart-send-telemetry-node.md)

* Para obter informações sobre os SDKs que pode utilizar para enviar mensagens dispositivo-a-nuvem, consulte [SDKs Azure IoT](iot-hub-devguide-sdks.md).