---
title: Integrar com o Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Veja como configurar rotas de eventos de Azure Digital Twins a Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3bc821f31e0463000c9089e48818249f2c046ede
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103000"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integre as Gémeas Digitais Azure com Insights da Série De Tempo Azure

Neste artigo, você vai aprender a integrar Azure Digital Twins com [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

A solução descrita neste artigo permitirá reunir e analisar dados históricos sobre a sua solução IoT. O Azure Digital Twins é indicado para transferir dados para o Time Series Insights, uma vez que lhe permite correlacionar múltiplos fluxos de dados e uniformizar as informações antes de as enviar para o Time Series Insights. 

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

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e filtrar notificações de atualização de duplos digitais

As instâncias Azure Digital Twins podem emitir [eventos de atualização dupla](how-to-interpret-event-data.md) sempre que o estado de um gémeo é atualizado. Nesta secção, irá criar uma rota de [**eventos**](concepts-route-events.md) Azure Digital Twins que irá direcionar estes eventos de atualização para a Azure [Event Hubs](../event-hubs/event-hubs-about.md) para posterior processamento.

O Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](./tutorial-end-to-end.md) num cenário em que um termómetro é usado para atualizar um atributo de temperatura num gémeo digital que representa uma sala. Este padrão baseia-se nas atualizações gémeas, em vez de encaminhar a telemetria de um dispositivo IoT, o que lhe dá flexibilidade para alterar a fonte de dados subjacente sem precisar de atualizar a lógica do Time Series Insights.

1. Em primeiro lugar, crie um espaço de nomes de centro de eventos que receberá eventos da sua instância Azure Digital Twins. Pode utilizar as instruções Azure CLI abaixo ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md). Para ver que regiões apoiam os Centros de Eventos, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Crie um centro de eventos dentro do espaço de nomes para receber eventos de twin change. Especifique um nome para o centro de eventos.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Criar uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber. Especifique um nome para a regra.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Crie um [ponto final](concepts-route-events.md#create-an-endpoint) Azure Digital Twins que ligue o seu centro de eventos à sua instância Azure Digital Twins.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Crie uma [rota ](concepts-route-events.md#create-an-event-route) no Azure Digital Twins para enviar eventos de atualização de duplos digitais para o ponto final. O filtro nesta rota só permitirá que mensagens de atualização duplas sejam transmitidas para o seu ponto final.

    >[!NOTE]
    >Atualmente, existe um **problema conhecido** no Cloud Shell que está a afetar os seguintes grupos de comando: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Para resolver, execute `az login` no Cloud Shell antes de executar o comando ou utilize a [CLI local](/cli/azure/install-azure-cli) em vez do Cloud Shell. Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Antes de seguir em frente, tome nota do seu grupo de nomes e *recursos* *do Event Hubs,* pois irá usá-los novamente para criar outro centro de eventos mais tarde neste artigo.

## <a name="create-a-function-in-azure"></a>Criar uma função no Azure

Em seguida, você usará Azure Functions para criar uma **função desencadeada por Event Hubs** dentro de uma aplicação de função. Pode utilizar a aplicação de função criada no tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](./tutorial-end-to-end.md)ou a sua própria. 

Esta função converterá esses eventos de atualização dupla da sua forma original como documentos do JSON Patch para objetos JSON, contendo apenas valores atualizados e adicionados dos seus gémeos.

Para obter mais informações sobre a utilização de Centros de Eventos com Funções Azure, consulte [*o gatilho Azure Event Hubs para Funções Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro da sua aplicação de função publicada, adicione uma nova função chamada **ProcessDTUpdatetoTSI** com o seguinte código.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Poderá ter de adicionar os pacotes ao seu projeto utilizando o `dotnet add package` comando ou o gestor de pacotes Visual Studio NuGet.

Em seguida, **publique** a nova função Azure. Para obter instruções sobre como [*fazê-lo, consulte Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md#publish-the-function-app-to-azure).

Olhando para o futuro, esta função enviará os objetos JSON que cria para um segundo centro de eventos, que irá ligar ao Time Series Insights. Vai criar o centro de eventos na próxima secção.

Mais tarde, também definirá algumas variáveis ambientais que esta função irá usar para se conectar aos seus próprios centros de eventos.

## <a name="send-telemetry-to-an-event-hub"></a>Enviar telemetria para um hub de eventos

Irá agora criar um segundo centro de eventos e configurar a sua função para transmitir a sua saída para o centro de eventos. Este hub de eventos será ligado ao Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar o segundo hub de eventos, pode utilizar as instruções Azure CLI abaixo, ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md).

1. Prepare o seu *espaço de nomes e* nome de grupo de *recursos* do Evento Hubs anteriormente neste artigo

2. Criar um novo centro de eventos. Especifique um nome para o centro de eventos.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Criar uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e receber. Especifique um nome para a regra.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Configurar a função

Em seguida, terá de definir variáveis ambientais na sua aplicação de função anteriormente, contendo as cordas de conexão para os centros de eventos que criou.

### <a name="set-the-twins-event-hub-connection-string"></a>Definir a cadeia de ligação do hub de eventos do Twins

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)Twins, usando as regras de autorização que criou acima para o hub Twins.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Utilize o valor *principal doConnectionString a* partir do resultado para criar uma definição de aplicação na sua aplicação de função que contenha a sua cadeia de ligação:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Configurar a cadeia de ligação do hub de eventos do Time Series Insights

1. Obtenha a cadeia de [conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)TSI, utilizando as regras de autorização acima criadas para o hub time series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Utilize o valor *principal doConnectionString a* partir do resultado para criar uma definição de aplicação na sua aplicação de função que contenha a sua cadeia de ligação:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e ligar uma instância do Time Series Insights

Em seguida, irá configurar uma instância time series insights para receber os dados do seu segundo centro de eventos (TSI). Siga os passos abaixo, e para mais detalhes sobre este processo, consulte [*Tutorial: Crie um ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorial-set-up-environment.md).

1. No portal Azure, comece a criar um ambiente de Insights de Séries Tempo. 
    1. Selecione o nível de preços **da Gen2(L1).**
    2. Terá de escolher uma **série de identificação para** este ambiente. O ID da sua série de tempo pode ser até três valores que utilizará para pesquisar os seus dados em Time Series Insights. Para este tutorial, pode utilizar **$dtId.** Leia mais sobre a seleção de um valor de ID nas [*melhores práticas para escolher um ID da Série De Tempo.*](../time-series-insights/how-to-select-tsid.md)
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="O portal de criação UX para um ambiente de Insights de Séries Tempo. Selecione a sua subscrição, grupo de recursos e localização a partir das respetivas reduções e escolha um nome para o seu ambiente." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="O portal de criação UX para um ambiente de Insights de Séries Tempo. O nível de preços da Gen2(L1) é selecionado e o nome de propriedade da série de tempo é $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Selecione **Seguinte: Event Source** e selecione as informações do seu hub de eventos TSI de mais cedo. Também terá de criar um novo grupo de consumidores de Eventos Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="O portal de criação UX para uma fonte de eventos de time series Insights ambiente. Você está criando uma fonte de evento com a informação do centro de eventos de cima. Está também a criar um novo grupo de consumidores." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Comece a enviar dados de IoT para Azure Digital Twins

Para começar a enviar dados para a Time Series Insights, terá de começar a atualizar as propriedades gémeas digitais em Azure Digital Twins com a alteração dos valores de dados. Utilize o comando [de atualização az dt twin.](/cli/azure/dt/twin#az_dt_twin_update)

Se estiver a utilizar o tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta)*](tutorial-end-to-end.md)para ajudar na configuração do ambiente, pode começar a enviar dados IoT simulados executando o projeto *DeviceSimulator* a partir da amostra. As instruções estão no [*Configure e executam a*](tutorial-end-to-end.md#configure-and-run-the-simulation) secção de simulação do tutorial.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizar dados no Time Series Insights

Agora, os dados devem estar a fluir para o seu exemplo de Time Series Insights, pronto para ser analisado. Siga os passos abaixo para explorar os dados que estão a chegar.

1. Abra o ambiente de Insights de Séries Tempotadas no [portal Azure](https://portal.azure.com) (pode pesquisar o nome do seu ambiente na barra de pesquisa do portal). Visite o *URL do Time Series Insights Explorer* mostrado na visão geral do exemplo.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Selecione o URL do explorador de insights de séries temporilhais no separador de visão geral do seu ambiente de Insights de Séries Tempotuais":::

2. No explorador, verá os seus três gémeos da Azure Digital Twins mostrados à esquerda. Selecione _**o termóstato67**_, selecione **a temperatura** e acerte **.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Selecione **termóstato67**, selecione **temperatura***, e bata **add**":::

3. Deve agora ver as leituras iniciais da temperatura do termóstato, como mostra abaixo. Essa mesma leitura de temperatura é atualizada para *o quarto21* e *o piso 1,* e você pode visualizar esses fluxos de dados em conjunto.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Os dados iniciais da temperatura são grafados no explorador de TSI. É uma linha de valores aleatórios entre 68 e 85":::

4. Se permitir que a simulação seja executada por muito mais tempo, a sua visualização será algo parecido com isto:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Os dados de temperatura para cada gémeo são grafados em três linhas paralelas de cores diferentes.":::

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais são armazenados por padrão como uma hierarquia plana em Time Series Insights, mas podem ser enriquecidos com informação de modelo e uma hierarquia multi-nível para organização. Para saber mais sobre este processo, leia: 

* [*Tutorial: Definir e aplicar um modelo*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte as seguintes referências:

* [*Como fazer: Gerir um gémeo digital*](./how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](./how-to-query-graph.md)