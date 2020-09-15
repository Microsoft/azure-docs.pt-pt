---
title: Integrar com o Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Veja como configurar rotas de eventos de Azure Digital Twins a Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6c5c9b00ec3309638a7c5618e5995c8c5f07b11
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564376"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integre as Gémeas Digitais Azure com Insights da Série De Tempo Azure

Neste artigo, você vai aprender a integrar Azure Digital Twins com [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

A solução descrita neste artigo permitirá reunir e analisar dados históricos sobre a sua solução IoT. O Azure Digital Twins é um ótimo ajuste para alimentar dados em Time Series Insights, pois permite-lhe correlacionar vários fluxos de dados e normalizar as suas informações antes de enviá-los para a Time Series Insights. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma relação com a Time Series Insights, precisa de ter uma **instância Azure Digital Twins**. Este caso deve ser configurado com a capacidade de atualizar informações digitais de gémeos com base em dados, uma vez que terá de atualizar informações gémeas algumas vezes para ver esses dados rastreados no Time Series Insights. 

Se ainda não tiver esta configuração, pode criá-la seguindo o Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](./tutorial-end-to-end.md). O tutorial irá acompanhá-lo através da criação de um exemplo de Azure Digital Twins que funciona com um dispositivo IoT virtual para desencadear atualizações digitais de twin.

## <a name="solution-architecture"></a>Arquitetura de soluções

Você vai anexar Time Series Insights a Azure Digital Twins através do caminho abaixo.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uma visão dos serviços da Azure num cenário de ponta a ponta, destacando a Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e filtrar para notificações de duas atualizações

As instâncias Azure Digital Twins podem emitir [eventos de atualização dupla](how-to-interpret-event-data.md) sempre que o estado de um gémeo é atualizado. Nesta secção, irá criar uma rota de [**eventos**](concepts-route-events.md) Azure Digital Twins que irá direcionar estes eventos de atualização para a Azure [Event Hubs](../event-hubs/event-hubs-about.md) para posterior processamento.

O Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](./tutorial-end-to-end.md) num cenário em que um termómetro é usado para atualizar um atributo de temperatura num gémeo digital que representa uma sala. Este padrão baseia-se nas atualizações gémeas, em vez de encaminhar a telemetria de um dispositivo IoT, o que lhe dá flexibilidade para alterar a fonte de dados subjacente sem precisar de atualizar a lógica do Time Series Insights.

1. Em primeiro lugar, crie um espaço de nomes de centro de eventos, que receberá eventos da sua instância Azure Digital Twins. Pode utilizar as instruções Azure CLI abaixo ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Crie um centro de eventos dentro do espaço de nomes.

    ```azurecli
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Criar uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber.

    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Crie um [ponto final](concepts-route-events.md#create-an-endpoint) Azure Digital Twins que ligue o tópico da grelha de eventos à sua instância Azure Digital Twins.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Crie uma [rota](concepts-route-events.md#create-an-event-route) em Azure Digital Twins para enviar eventos de atualização dupla para o seu ponto final. O filtro nesta rota só permitirá que mensagens de atualização duplas sejam transmitidas para o seu ponto final.

    >[!NOTE]
    >Existe atualmente um **problema conhecido** na Cloud Shell que afeta estes grupos de comando: . . . . . . . . . . . . `az dt route` . `az dt model` `az dt twin` .
    >
    >Para resolver, ou corra `az login` em Cloud Shell antes de executar o comando, ou use o [CLI local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em vez de Cloud Shell. Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Antes de seguir em frente, tome nota do seu grupo de nomes e *recursos* *do Event Hubs,* pois irá usá-los novamente para criar outro centro de eventos mais tarde neste artigo.

## <a name="create-an-azure-function"></a>Criar uma função do Azure 

Em seguida, irá criar uma função desencadeada por Event Hubs dentro de uma aplicação de função. Pode utilizar a aplicação de função criada no tutorial de ponta a ponta[*(Tutorial: Conecte uma solução de ponta a ponta),*](./tutorial-end-to-end.md)ou a sua própria. 

Esta função converterá esses eventos de atualização dupla da sua forma original como documentos do JSON Patch para objetos JSON, contendo apenas valores atualizados e adicionados dos seus gémeos.

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
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
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

Mais tarde, também definirá algumas variáveis ambientais que esta função irá usar para se conectar aos seus próprios centros de eventos.

## <a name="send-telemetry-to-an-event-hub"></a>Enviar telemetria para um centro de eventos

Irá agora criar um segundo centro de eventos e configurar a sua função para transmitir a sua saída para o centro de eventos. Este centro de eventos será então ligado ao Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar o segundo hub de eventos, pode utilizar as instruções Azure CLI abaixo, ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md).

1. Prepare o seu *espaço de nomes e* nome de grupo de *recursos* do Evento Hubs anteriormente neste artigo

2. Criar um novo centro de eventos
    ```azurecli
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Criar uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber
    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Configure a sua função

Em seguida, terá de definir variáveis ambientais na sua aplicação de função anteriormente, contendo as cordas de conexão para os centros de eventos que criou.

### <a name="set-the-twins-event-hub-connection-string"></a>Definir a cadeia de conexão do hub de evento twins

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)Twins, usando as regras de autorização que criou acima para o hub Twins.

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Utilize a cadeia de ligação que obtém como resultado para criar uma definição de aplicação na sua aplicação de função que contenha a sua cadeia de ligação:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Desa parte da cadeia de ligação do centro de eventos Time Series Insights

1. Obtenha a cadeia de [conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)TSI, utilizando as regras de autorização acima criadas para o hub time series Insights:

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Na sua aplicação de função, crie uma definição de aplicação que contenha a sua cadeia de ligação:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e ligar uma instância de Insights de Séries De Tempo

Em seguida, irá configurar uma instância time series insights para receber os dados do seu segundo centro de eventos. Siga os passos abaixo, e para mais detalhes sobre este processo, consulte [*Tutorial: Crie um ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. No portal Azure, comece a criar um recurso Time Series Insights. 
    1. Selecione o nível de preços **PAYG (Pré-visualização).**
    2. Terá de escolher uma **série de identificação para** este ambiente. O ID da sua série de tempo pode ser até três valores que utilizará para pesquisar os seus dados em Time Series Insights. Para este tutorial, pode utilizar **$dtId.** Leia mais sobre a seleção de um valor de ID nas [*melhores práticas para escolher um ID da Série De Tempo.*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="O portal de criação UX para um ambiente de Insights de Séries Tempo. O nível de preços PAYG (Preview) é selecionado e o nome de propriedade da série de tempo é $dtId":::

2. Selecione **Seguinte: Fonte de eventos** e selecione as informações do Event Hubs a partir de cima. Também terá de criar um novo grupo de consumidores de Eventos Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="O portal de criação UX para uma fonte de eventos de time series Insights ambiente. Você está criando uma fonte de evento com a informação do centro de eventos de cima. Está também a criar um novo grupo de consumidores.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Comece a enviar dados de IoT para Azure Digital Twins

Para começar a enviar dados para a Time Series Insights, terá de começar a atualizar as propriedades gémeas digitais em Azure Digital Twins com a alteração dos valores de dados. Utilize o comando [de atualização az dt twin.](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Se estiver a utilizar o tutorial de ponta a ponta[*(Tutorial: Conecte uma solução de ponta a ponta)*](tutorial-end-to-end.md)para ajudar na configuração do ambiente, pode começar a enviar dados IoT simulados executando o projeto *DeviceSimulator* a partir da amostra. As instruções estão no [*Configure e executam a*](tutorial-end-to-end.md#configure-and-run-the-simulation) secção de simulação do tutorial.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualize os seus dados em Time Series Insights

Agora, os dados devem estar a fluir para o seu exemplo de Time Series Insights, pronto para ser analisado. Siga os passos abaixo para explorar os dados que estão a chegar.

1. Abra a sua instância de Insights de Séries Tempotizadas no [portal Azure](https://portal.azure.com) (pode pesquisar o nome do seu caso na barra de pesquisa do portal). Visite o *URL do Time Series Insights Explorer* mostrado na visão geral do exemplo.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Selecione o URL do explorador de insights de séries temporilhais no separador de visão geral do seu ambiente de Insights de Séries Tempotuais":::

2. No explorador, verá os seus três gémeos da Azure Digital Twins mostrados à esquerda. Selecione _**o termóstato67**_, selecione **a temperatura**e acerte **.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Selecione **termóstato67**, selecione **temperatura***, e bata **add**":::

3. Deve agora ver as leituras iniciais da temperatura do termóstato, como mostra abaixo. Essa mesma leitura de temperatura é atualizada para *o quarto21* e *o piso 1,* e você pode visualizar esses fluxos de dados em conjunto.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Os dados iniciais da temperatura são grafados no explorador de TSI. É uma linha de valores aleatórios entre 68 e 85":::

4. Se permitir que a simulação seja executada por muito mais tempo, a sua visualização será algo parecido com isto:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Os dados de temperatura para cada gémeo são grafados em três linhas paralelas de cores diferentes.":::

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais são armazenados por padrão como uma hierarquia plana em Time Series Insights, mas podem ser enriquecidos com informação de modelo e uma hierarquia multi-nível para organização. Para saber mais sobre este processo, leia: 

* [*Tutorial: Definir e aplicar um modelo*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte as seguintes referências:

* [*Como fazer: Gerir um gémeo digital*](./how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](./how-to-query-graph.md)