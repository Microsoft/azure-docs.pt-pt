---
title: Integre-se com insights de séries temporências
titleSuffix: Azure Digital Twins
description: Veja como configurar rotas de eventos de Azure Digital Twins a Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131604"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integre gémeos digitais com insights da série de tempo Azure

Nesta referência, você aprenderá a integrar Azure Digital Twins com [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Esta solução permitir-lhe-á recolher e analisar dados históricos sobre a sua solução IoT. O Azure Digital Twins é um ótimo ajuste para alimentar dados em Time Series Insights, pois permite-lhe correlacionar vários fluxos de dados e normalizar as suas informações antes de enviá-los para a Time Series Insights. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Irá anexar insights da Série Tempo às Gémeas Digitais Azure através do caminho abaixo.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uma visão dos serviços da Azure num cenário de ponta a ponta, destacando a Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma instância Azure Digital Twins que pode atualizar informações gémeas algumas vezes para ver os dados rastreados no Time Series Insights. 
    * Se não tiver um, siga o Tutorial Azure Digital [*Twins: Conecte uma solução de ponta a ponta*](./tutorial-end-to-end.md) para configurar uma instância Azure Digital Twins e um dispositivo IoT virtual para gerar alterações gémeas.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e filtrar para notificações de duas atualizações

As instâncias Azure Digital Twins podem emitir [eventos de atualização dupla](how-to-interpret-event-data.md) sempre que o estado de um gémeo é atualizado. Você estará a criar uma rota que irá direcionar estes eventos de atualização para um centro de eventos para posterior processamento.

O Tutorial de Gémeos Digitais Azure: [*Ligue uma solução de ponta a ponta*](./tutorial-end-to-end.md) através de um cenário em que um termómetro é usado para atualizar um atributo de temperatura ligado ao gémeo de uma sala. Este padrão utiliza as atualizações gémeas, em vez de encaminhar a telemetria de um dispositivo IoT, o que lhe dá flexibilidade para alterar a fonte de dados subjacente sem precisar de atualizar a lógica do Time Series Insights.

1. Crie um espaço de nomes de hub de eventos, que receberá eventos da sua instância Azure Digital Twins. Pode utilizar as instruções Azure CLI abaixo ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Crie um hub de eventos.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Criar uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Crie um ponto final para ligar o tópico da grelha de eventos à Azure Digital Twins.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Crie uma rota em Azure Digital Twins para enviar eventos de atualização dupla para o seu ponto final. O filtro nesta rota só permitirá que mensagens de atualização duplas sejam transmitidas para o seu ponto final.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Criar uma função do Azure 

Em seguida, vai criar uma função desencadeada por Event Hubs dentro de uma nova aplicação de função, a sua aplicação de função a partir do tutorial de ponta a ponta[*(Tutorial: Conecte uma solução de ponta a ponta).*](./tutorial-end-to-end.md) Esta função converterá essas atualizações da sua forma original como documentos de correção JSON para objetos JSON contendo apenas valores atualizados e adicionados dos seus gémeos.

Para obter mais informações sobre a utilização de Centros de Eventos com funções Azure, consulte [*o gatilho Azure Event Hubs para Funções Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro da sua aplicação de função publicada, substitua o código de função pelo seguinte código.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

A partir daqui, a função enviará então os objetos JSON que cria para um segundo centro de eventos, que irá ligar ao Time Series Insights.

## <a name="send-telemetry-to-an-event-hub"></a>Enviar telemetria para um centro de eventos

Irá agora criar um segundo hub de eventos e configurar a sua função para transmitir a sua saída para o centro de eventos. Este centro de eventos será então ligado ao Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode utilizar as instruções Azure CLI abaixo ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md).

1. Prepare o seu espaço de nome de centro de eventos e nome de grupo de recursos de anteriormente 

2. Criar um hub de eventos
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Criar uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Configure a sua função

Você precisará definir uma variável ambiental na sua aplicação de função de anterior, contendo a sua cadeia de conexão de hub de evento.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Desa parte da cadeia de ligação do centro de eventos Time Series Insights

1. Obtenha a [cadeia de conexão do centro](../event-hubs/event-hubs-get-connection-string.md) de eventos para as regras de autorização que criou acima para o hub time series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. Na sua aplicação de função, crie uma definição de aplicação que contenha a sua cadeia de ligação:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Definir a cadeia de conexão do hub de evento twins

1. Obtenha a [cadeia de conexão do centro](../event-hubs/event-hubs-get-connection-string.md) de eventos para as regras de autorização que criou acima para o centro dos gémeos.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Na sua aplicação de função, crie uma definição de aplicação que contenha a sua cadeia de ligação:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e ligar uma instância de Insights de Séries De Tempo

Em seguida, irá configurar uma instância time series insights para receber os dados do seu segundo centro de eventos. Para mais detalhes sobre este processo consulte [ *Tutorial: Crie um ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. No portal Azure, comece a criar um recurso Time Series Insights. 
    1. Selecione o nível de preços **PAYG (Pré-visualização).**
    2. Terá de escolher uma série de identificação para este ambiente. O ID da sua série de tempo pode ser até três valores que utilizará para pesquisar os seus dados em Time Series Insights. Para este tutorial, pode utilizar **$dtId.** Leia mais sobre a seleção de um valor de ID nas [*melhores práticas para escolher um ID da Série De Tempo.*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="O portal de criação UX para um ambiente de Insights de Séries Tempo. O nível de preços PAYG (Preview) é selecionado e o nome de propriedade da série de tempo é $dtId":::

2. Selecione **Seguinte: Fonte de eventos** e selecione as informações do Event Hubs a partir de cima. Também terá de criar um novo grupo de consumidores de Eventos Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="O portal de criação UX para uma fonte de eventos de time series Insights ambiente. Você está criando uma fonte de evento com a informação do centro de eventos de cima. Está também a criar um novo grupo de consumidores.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Comece a enviar dados de IoT para Azure Digital Twins

Para começar a enviar dados para o Time Series Insights, terá de começar a atualizar as propriedades gémeas digitais com a alteração dos valores de dados. Utilize o comando [de atualização az dt twin.](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update)

Se estiver a utilizar o tutorial de ponta a ponta para ajudar na configuração do ambiente, pode começar a enviar dados IoT simulados executando o `DeviceSimulator` projeto a partir do Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md). As instruções estão no [*Configure e executam a*](tutorial-end-to-end.md#configure-and-run-the-simulation) secção de simulação do tutorial.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualize os seus dados em Time Series Insights

Agora, os dados devem estar a fluir para o seu exemplo de Time Series Insights, pronto para ser analisado. Siga os passos abaixo para explorar os dados que estão a chegar.

1. Abra a sua instância de Insights de Séries Tempoturais no portal Azure. Visite o URL do Time Series Insights Explorer mostrado na visão geral.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Clique no URL do explorador de insights de séries temporilhais no separador de visão geral do seu ambiente de Insights de Séries Tempotuais":::

2. No explorador, verão os seus três gémeos mostrados à esquerda. Clique no **termóstato67,** selecione **patch_value**, e clique em **adicionar**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Clique em **termóstato67**, selecione **temperatura***, e clique em **add**":::

3. Deve agora ver as leituras iniciais da temperatura do termóstato, como mostra abaixo. Essa mesma leitura de temperatura é atualizada para o quarto21 e o piso 1, e você pode visualizar esses fluxos de dados em conjunto.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Os dados iniciais da temperatura são grafados no explorador de TSI. É uma linha de valores aleatórios entre 68 e 85":::

4. Se permitir que a simulação seja executada por muito mais tempo, a sua visualização será algo parecido com isto:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Os dados de temperatura para cada gémeo são grafados em três linhas paralelas de cores diferentes.":::

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais são armazenados por padrão como uma hierarquia plana em Time Series Insights, mas podem ser enriquecidos com informação de modelo e uma hierarquia multi-nível para organização. Para saber mais sobre este processo, leia: 

* [*Tutorial: Definir e aplicar um modelo*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte as seguintes referências:

* [*Como fazer: Gerir um gémeo digital*](./how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](./how-to-query-graph.md)