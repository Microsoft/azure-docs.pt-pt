---
title: Compreenda o encaminhamento de mensagens Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - como usar o encaminhamento de mensagens para enviar mensagens de dispositivo-nuvem. Inclui informações sobre o envio de dados de telemetria e não-telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370462"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Use o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-cloud para diferentes pontos finais

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O encaminhamento de mensagens permite-lhe enviar mensagens dos seus dispositivos para serviços de nuvem de forma automatizada, escalável e fiável. O encaminhamento de mensagens pode ser utilizado para: 

* **Enviar mensagens de telemetria de dispositivos, bem como eventos,** nomeadamente, eventos de ciclo de vida do dispositivo e eventos de mudança de dispositivo para os pontos finais incorporados e personalizados. Saiba mais sobre [os pontos finais de encaminhamento](#routing-endpoints).

* **Filtrando os dados antes de encaminhar para vários pontos finais** aplicando consultas ricas. O encaminhamento de mensagens permite-lhe consultar as propriedades da mensagem e do corpo da mensagem, bem como as duas etiquetas do dispositivo e as propriedades gémeas do dispositivo. Saiba mais sobre a utilização de [consultas no encaminhamento de mensagens.](iot-hub-devguide-routing-query-syntax.md)

O IoT Hub precisa de ter acesso a estes pontos finais de serviço para o encaminhamento de mensagens para o trabalho. Se configurar os seus pontos finais através do portal Azure, as permissões necessárias são adicionadas para si. Certifique-se de configurar os seus serviços para suportar a entrada esperada. Por exemplo, se estiver a usar os Hubs de Eventos como um ponto final personalizado, tem de configurar as **unidades** de entrada para esse centro de eventos para que possa lidar com a entrada de eventos que planeia enviar através do encaminhamento de mensagens IoT Hub. Da mesma forma, ao utilizar uma fila de ônibus de serviço como ponto final, deve configurar o **tamanho máximo** para garantir que a fila pode manter todos os dados inalizados, até que seja esganado pelos consumidores. Quando configurar pela primeira vez a sua solução IoT, poderá ter de monitorizar os seus pontos finais adicionais e efazer os ajustes necessários para a carga real.

O IoT Hub define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens dispositivo-nuvem para interoperabilidade através de protocolos. Se uma mensagem corresponder a várias rotas que apontam para o mesmo ponto final, o IoT Hub entrega mensagem a esse ponto final apenas uma vez. Portanto, não precisa de configurar a duplicação na sua fila de ônibus de serviço ou tópico. Nas filas divididas, a afinidade da partilha garante a encomenda de mensagens. Utilize este tutorial para aprender a [configurar o encaminhamento de mensagens](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos finais de encaminhamento

Um hub IoT tem um ponto incorporado padrão **(mensagens/eventos)** compatível com Os Hubs de Eventos. Pode criar [pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para encaminhar mensagens, ligando outros serviços na sua subscrição ao IoT Hub. 

Cada mensagem é encaminhada para todos os pontos finais cujas consultas de encaminhamento correspondem. Por outras palavras, uma mensagem pode ser encaminhada para vários pontos finais.

O IoT Hub suporta atualmente os seguintes serviços como pontos finais personalizados:

### <a name="built-in-endpoint"></a>Ponto final incorporado

Pode utilizar a integração padrão dos Centros de [Eventos e SDKs](iot-hub-devguide-messages-read-builtin.md) para receber mensagens dispositivo-a-nuvem a partir do ponto final incorporado **(mensagens/eventos).** Uma vez criada uma Rota, os dados param de fluir para o ponto final incorporado, a menos que uma Rota seja criada para esse ponto final.

### <a name="azure-storage"></a>Storage do Azure

Existem dois serviços de armazenamento IoT Hub pode encaminhar mensagens para -- [Contas azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). As contas de armazenamento do Lago De Dados Azure são contas de armazenamento [hierárquicas](../storage/blobs/data-lake-storage-namespace.md)ativadas por espaço de nome construídos em cima do armazenamento de blob. Ambos usam bolhas para o seu armazenamento.

O IoT Hub suporta a escrita de dados para o Armazenamento Azure no formato [Apache Avro,](https://avro.apache.org/) bem como no formato JSON. O padrão é AVRO. O formato de codificação só pode ser definido quando o ponto final de armazenamento blob estiver configurado. O formato não pode ser editado para um ponto final existente. Ao utilizar a codificação JSON, deve definir o conteúdoType para **aplicação/json** e contentEncoding para **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens . Ambos os valores são insensíveis aos casos. Se a codificação de conteúdo não estiver definida, o IoT Hub escreverá as mensagens no formato codificado base 64. Pode selecionar o formato de codificação utilizando o IoT Hub Create ou Update REST API, especificamente o [RoutingStorageContainerProperties,](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)o portal [Azure, o Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)ou o [Azure PowerShell.](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0) O diagrama seguinte mostra como selecionar o formato de codificação no portal Azure.

![Codificação de ponto final de armazenamento blob](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

O IoT Hub emite mensagens e escreve dados para armazenamento sempre que o lote atinge um determinado tamanho ou uma certa quantidade de tempo tenha decorrido. O IoT Hub não se aplica à seguinte convenção de nomeação de ficheiros: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Pode utilizar qualquer convenção de nomeação de ficheiros, no entanto deve utilizar todos os tokens listados. O IoT Hub escreverá a uma bolha vazia se não houver dados para escrever.

Recomendamos a listagem das bolhas ou ficheiros e, em seguida, iterando sobre eles, para garantir que todas as bolhas ou ficheiros são lidos sem fazer quaisquer pressupostos de partição. A gama de divisórias pode potencialmente mudar durante uma [falha iniciada pela Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) ou falha manual do IoT Hub . [manual failover](iot-hub-ha-dr.md#manual-failover) Pode utilizar a [Lista Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para enumerar a lista de blobs ou [Lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) para a lista de ficheiros. Por favor, veja a seguinte amostra como orientação.

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

> [!NOTE]
> Se a sua conta de armazenamento tiver configurações de firewall que restringem a conectividade do IoT Hub, considere utilizar a [microsoft confiável primeira exceção de primeira parte](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (disponível em regiões selecionadas para centros IoT com identidade de serviço gerida).

Para criar uma conta de armazenamento compatível com o Azure Data Lake Gen2, crie uma nova conta de armazenamento V2 e *selecione ativada* no campo *hierárquico* de espaço de nome no separador **Avançado,** como mostra a seguinte imagem:

![Selecione armazenamento Azure Date Lake Gen2](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Filas de ônibus de serviço e tópicos de ônibus de serviço

As filas de ônibus de serviço e tópicos usados como pontos finais do Hub IoT não devem ter **Sessões** ou **Deteção duplicada** ativadas. Se alguma dessas opções estiver ativada, o ponto final aparece como **Incontactável** no portal Azure.

> [!NOTE]
> Se o seu recurso de ônibus de serviço tiver configurações de firewall que restringem a conectividade do IoT Hub, considere usar [a microsoft confiável primeira exceção de primeira parte](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) (disponível em regiões selecionadas para centros IoT com identidade de serviço gerida).


### <a name="event-hubs"></a>Event Hubs

Além do ponto final compatível com os Hubs incorporados no evento, também pode direcionar dados para pontos finais personalizados de Hubs de Eventos tipo. 

> [!NOTE]
> Se o seu recurso de hubs de eventos tiver configurações de firewall que restringem a conectividade do IoT Hub, considere usar [a microsoft confiável primeira exceção de primeira parte](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) (disponível em regiões selecionadas para centros IoT com identidade de serviço gerida).


## <a name="reading-data-that-has-been-routed"></a>Dados de leitura que foram encaminhados

Pode configurar uma rota seguindo este [tutorial.](tutorial-routing.md)

Utilize os seguintes tutoriais para aprender a ler a mensagem a partir de um ponto final.

* Leitura do [ponto final incorporado](quickstart-send-telemetry-node.md)

* Leitura do [armazenamento blob](../storage/blobs/storage-blob-event-quickstart.md)

* Leitura de Centros de [Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Leitura de filas de [ônibus de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Ler a partir de tópicos de [ônibus de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Rota de recuo

A rota de recuo envia todas as mensagens que não satisfazem as condições de consulta em nenhuma das rotas existentes para os Hubs embutidos **(mensagens/eventos),** que são compatíveis com os Hubs de [Eventos.](/azure/event-hubs/) Se o encaminhamento de mensagens estiver ligado, pode ativar a capacidade de rota de recuo. Uma vez criada uma rota, os dados param de fluir para o ponto final incorporado, a menos que seja criada uma rota para esse ponto final. Se não houver rotas para o ponto de encontro incorporado e uma rota de recuo estiver ativada, apenas mensagens que não correspondam a quaisquer condições de consulta nas rotas serão enviadas para o ponto final incorporado. Além disso, se todas as rotas existentes forem eliminadas, a rota de recuo deve ser ativada para receber todos os dados no ponto final incorporado.

Pode ativar/desativar a rota de recuo na lâmina de encaminhamento de mensagens >portal Azure. Também pode utilizar o Gestor de Recursos Azure para [fallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para utilizar um ponto final personalizado para a rota de recuo.

## <a name="non-telemetry-events"></a>Eventos de não-telemetria

Além da telemetria do dispositivo, o encaminhamento de mensagens também permite o envio de eventos de mudança de dispositivos twin change, eventos de ciclo de vida do dispositivo e eventos de mudança de twin digital (em pré-visualização pública). Por exemplo, se for criada uma rota com um conjunto de fontes de dados para **dispositivos de mudança de twin change,** o IoT Hub envia mensagens para o ponto final que contêm a alteração no dispositivo twin. Da mesma forma, se uma rota for criada com um conjunto de fontede dados para dispositivos de ciclo de **vida,** o IoT Hub envia uma mensagem indicando se o dispositivo foi apagado ou criado. Finalmente, como parte da [pré-visualização pública IoT Plug e Play,](../iot-pnp/overview-iot-plug-and-play.md)um desenvolvedor pode criar rotas com conjunto de fonte de dados para **eventos de mudança de twin digital** e o IoT Hub envia mensagens sempre que uma [propriedade](../iot-pnp/iot-plug-and-play-glossary.md) digital twin é definida ou alterada, um [twin digital](../iot-pnp/iot-plug-and-play-glossary.md) é substituído, ou quando um evento de mudança acontece para o dispositivo twin subjacente.

[O IoT Hub integra-se também com](iot-hub-event-grid.md) a Azure Event Grid para publicar eventos de dispositivos para apoiar integrações em tempo real e automatização de fluxos de trabalho com base nestes eventos. Veja [as principais diferenças entre o encaminhamento de mensagens e](iot-hub-event-grid-routing-comparison.md) a Grelha de Eventos para saber o que funciona melhor para o seu cenário.

## <a name="testing-routes"></a>Rotas de teste

Quando criar uma nova rota ou editar uma rota existente, deve testar a consulta de rota com uma mensagem de amostra. Pode testar rotas individuais ou testar todas as rotas de uma só vez e nenhuma mensagem é encaminhada para os pontos finais durante o teste. O portal Azure, o Azure Resource Manager, o Azure PowerShell e o Azure CLI podem ser utilizados para testes. Os resultados ajudam a identificar se a mensagem da amostra correspondia à consulta, a mensagem não correspondia à consulta, ou se o teste não podia ser executado porque a mensagem da amostra ou a sintaxe de consulta estão incorretas. Para saber mais, consulte [A Rota de Teste](/rest/api/iothub/iothubresource/testroute) e Teste todas as [rotas.](/rest/api/iothub/iothubresource/testallroutes)

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Encomendar garantias com pelo menos uma vez entregue

Garantias de encaminhamento de mensagens IoT Hub ordenadas e pelo menos uma vez entregue mensagens para os pontos finais. Isto significa que pode haver mensagens duplicadas e uma série de mensagens podem ser retransmitidas honrando a ordem original da mensagem. Por exemplo, se a ordem de mensagem original for [1,2,3,4], poderá receber uma sequência de mensagens como [1,2,2,2,2,3,1,2,3,4]. A garantia de encomenda é que, se alguma vez receber mensagem [1], será sempre seguida por [2,3,4].

Para manusear duplicados de mensagens, recomendamos a estampagem de um identificador único nas propriedades de aplicação da mensagem no ponto de origem, que normalmente é um dispositivo ou um módulo. O serviço que consome as mensagens pode lidar com mensagens duplicadas utilizando este identificador.

## <a name="latency"></a>Latência

Ao direcionar as mensagens de telemetria dispositivo-nuvem utilizando pontos finais incorporados, há um ligeiro aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio da latência é inferior a 500 ms. Pode monitorizar a latência utilizando **o Encaminhamento: latência de mensagens/eventos** ou **d2c.endpoints.latency.builtIn.events** IoT Hub metric. Criar ou apagar qualquer rota após a primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

O IoT Hub fornece várias métricas relacionadas com o encaminhamento e pontos finais para lhe dar uma visão geral da saúde do seu hub e mensagens enviadas. Pode combinar informação a partir de múltiplas métricas para identificar a causa principal para problemas. Por exemplo, use **o Encaminhamento métrico: as mensagens de telemetria caíram** ou **d2c.telemetria.egress.caiu** para identificar o número de mensagens que foram retiradas quando não correspondiam a consultas em nenhuma das rotas e a rota de recuo foi desativada. [As métricas do IoT Hub](iot-hub-metrics.md) listam todas as métricas que são ativadas por padrão para o seu Hub IoT.

Você pode usar o REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de [saúde](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos finais. Recomendamos a utilização das [métricas do Hub IoT relacionadas](iot-hub-metrics.md) com a latência da mensagem de encaminhamento para identificar e depurar erros quando a saúde do ponto final está morta ou insalubre. Por exemplo, para centros de eventos do tipo ponto final, pode monitorizar **d2c.endpoints.latency.eventHubs**. O estado de um ponto final pouco saudável será atualizado para saudável quando o IoT Hub estabelecer um estado de saúde eventualmente consistente.

Utilizando os registos de diagnóstico das **rotas** nas [definições](../iot-hub/iot-hub-monitor-resource-health.md)de diagnóstico do Monitor Azure, pode rastrear erros que ocorrem durante a avaliação de uma consulta de encaminhamento e saúde de ponta final, como percebido pelo IoT Hub, por exemplo, quando um ponto final está morto. Estes registos de diagnóstico podem ser enviados para registos do Monitor Azure, Hubs de Eventos ou Armazenamento Azure para processamento personalizado.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar rotas de mensagens, consulte [as mensagens processio IoT Hub dispositivo-to-cloud usando rotas](tutorial-routing.md).

* [Como enviar mensagens de dispositivo-para-nuvem](quickstart-send-telemetry-node.md)

* Para obter informações sobre os SDKs pode utilizar para enviar mensagens de dispositivo-nuvem, consulte [SDKs Azure IoT](iot-hub-devguide-sdks.md).
