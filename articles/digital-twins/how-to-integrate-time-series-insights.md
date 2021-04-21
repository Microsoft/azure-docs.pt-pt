---
title: Integrar com o Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Veja como configurar rotas de eventos de Azure Digital Twins a Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783778"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integre as Gémeas Digitais Azure com Insights da Série De Tempo Azure

Neste artigo, você vai aprender a integrar Azure Digital Twins com [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

A solução descrita neste artigo permitirá reunir e analisar dados históricos sobre a sua solução IoT. O Azure Digital Twins é indicado para transferir dados para o Time Series Insights, uma vez que lhe permite correlacionar múltiplos fluxos de dados e uniformizar as informações antes de as enviar para o Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma relação com a Time Series Insights, terá de configurar os seguintes recursos:
* Um **hub IoT.** Para obter instruções, consulte a secção [*Criar um hub IoT*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) do quickstart *de envio de telemetria do IoT Hub.*
* Um **exemplo de Gémeos Digitais Azure**.
Para obter instruções, consulte [*Como fazer: Configurar uma instância e autenticação de Gémeos Digitais Azure*](./how-to-set-up-instance-portal.md).
* Um **modelo e um gémeo no exemplo Azure Digital Twins**.
Terá de atualizar algumas vezes as informações do Twin para ver os dados rastreados no Time Series Insights. Para obter instruções, consulte o [*Modelo e secção Twin*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) do artigo *como: Ingeste o artigo do hub IoT.*

> [!TIP]
> Neste artigo, as mudanças nos valores gémeos digitais que são vistos no Time Series Insights são atualizados manualmente para a simplicidade. No entanto, se quiser completar este artigo com dados simulados ao vivo, pode configurar uma função Azure que atualiza gémeos digitais com base em eventos de telemetria IoT a partir de um dispositivo simulado. Para obter instruções, siga [*como: Ingerir dados do IoT Hub,*](how-to-ingest-iot-hub-data.md)incluindo os passos finais para executar o simulador do dispositivo e validar que o fluxo de dados funciona.
>
> Mais tarde, procure outra DICA para lhe mostrar onde começar a executar o simulador do dispositivo e ter as suas funções Azure a atualizarem os gémeos automaticamente, em vez de enviarem comandos de atualização digital digital.


## <a name="solution-architecture"></a>Arquitetura de soluções

Você vai anexar Time Series Insights a Azure Digital Twins através do caminho abaixo.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagrama dos serviços Azure num cenário de ponta a ponta, destacando a Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Criar o espaço de nomes do hub de eventos

Antes de criar os centros de eventos, você vai primeiro criar um espaço de nome de centro de eventos que receberá eventos da sua instância Azure Digital Twins. Pode utilizar as instruções Azure CLI abaixo ou utilizar o portal Azure: [*Quickstart: Criar um centro de eventos utilizando o portal Azure*](../event-hubs/event-hubs-create.md). Para ver que regiões apoiam centros de eventos, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Se tiver um erro `BadRequest: The specified service namespace is invalid.` indicando, certifique-se de que o nome escolhido para o seu espaço de nome satisfaz os requisitos de nomeação descritos neste documento de referência: [Create Namespace](/rest/api/servicebus/create-namespace).

Você vai usar este espaço de nomes de centros de eventos para manter os dois centros de eventos que são necessários para este artigo:

  1. **Twins hub** - Centro de eventos para receber eventos de mudança dupla
  2. **Centro de séries de** tempo - centro de eventos para transmitir eventos para Time Series Insights

As próximas secções irão acompanhá-lo através da criação e configuração destes centros dentro do seu espaço de nomes de centros de eventos.

## <a name="create-twins-hub"></a>Criar centro de gémeos

O primeiro centro de eventos que vai criar neste artigo é o centro dos **gémeos.** Este centro de eventos receberá eventos de twin change da Azure Digital Twins.
Para configurar o centro dos gémeos, você completará os seguintes passos nesta secção:

1. Criar o centro dos gémeos
2. Criar uma regra de autorização para controlar permissões para o centro
3. Criar um ponto final em Azure Digital Twins que usa a regra de autorização para aceder ao hub
4. Crie uma rota em Azure Digital Twins que envia evento de twin updates para o centro de gémeos e gémeos conectados
5. Obtenha a corda de conexão do centro gémeos

Crie o **centro** gémeos com o seguinte comando CLI. Especifique um nome para o seu centro de gémeos.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Criar regra de autorização do hub de gémeos

Criar uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) com permissões de envio e receber. Especifique um nome para a regra.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Criar ponto final de hub gémeos

Crie um [ponto final](concepts-route-events.md#create-an-endpoint) Azure Digital Twins que ligue o seu centro de eventos à sua instância Azure Digital Twins. Especifique um nome para o seu ponto final do centro gémeos.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Criar rota de eventos de centro de gémeos

As instâncias Azure Digital Twins podem emitir [eventos de atualização dupla](how-to-interpret-event-data.md) sempre que o estado de um gémeo é atualizado. Nesta secção, irá criar uma rota de **eventos** Azure Digital Twins que irá direcionar estes eventos de atualização para o centro das gémeas para posterior processamento.

Crie uma [rota](concepts-route-events.md#create-an-event-route) em Azure Digital Twins para enviar eventos de atualização dupla para o seu ponto final a partir de cima. O filtro nesta rota só permitirá que mensagens de atualização duplas sejam transmitidas para o seu ponto final. Especifique um nome para a rota do evento do centro gémeos.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Obter cadeia de conexão de hub gémeos

Obtenha a [cadeia de conexão do hub de eventos gémeos,](../event-hubs/event-hubs-get-connection-string.md)usando as regras de autorização que criou acima para o centro dos gémeos.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Tome nota do valor **primário doConnectionString** do resultado para configurar a configuração da aplicação twins hub mais tarde neste artigo.

## <a name="create-time-series-hub"></a>Criar hub de séries de tempo

O segundo centro de eventos que vai criar neste artigo é o centro da **série de tempo.** Este é um centro de eventos que transmitirá os eventos Azure Digital Twins para Time Series Insights.
Para configurar o centro da série de tempo, você completará estes passos:

1. Criar o hub da série de tempo
2. Criar uma regra de autorização para controlar permissões para o centro
3. Obtenha a cadeia de conexão do hub da série de tempo

Mais tarde, quando criares a instância Time Series Insights, ligarás este hub da série de tempo como a fonte de evento para a instância Time Series Insights.

Crie o **hub da série de tempo** utilizando o seguinte comando. Especifique um nome para o hub da série de tempo.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Criar regra de autorização do hub de séries de tempo

Criar uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) com permissões de envio e receber. Especifique um nome para a regra do centro da série de tempo.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Obter cadeia de ligação de hub série de tempo

Obtenha a [cadeia de ligação do hub da série de tempo,](../event-hubs/event-hubs-get-connection-string.md)utilizando as regras de autorização acima criadas para o hub da série de tempo:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Tome nota do valor **primário doConnectionString** do resultado para configurar a definição da aplicação do hub da série de tempo mais tarde neste artigo.

Além disso, tome nota dos seguintes valores para usá-los mais tarde para criar uma instância de Insights de Séries tempor tomadas.
* Espaço de nomes do hub de eventos
* Nome do hub da série de tempo
* Regra do eixo da série de tempo

## <a name="create-a-function"></a>Criar uma função

Nesta secção, irá criar uma função Azure que converterá eventos de atualização dupla da sua forma original como documentos do JSON Patch para objetos JSON, contendo apenas valores atualizados e adicionados dos seus gémeos.

### <a name="step-1-create-function-app"></a>Passo 1: Criar app de função

Primeiro, crie um novo projeto de aplicação de função no Visual Studio. Para obter instruções sobre como fazê-lo, consulte a [**aplicação De configuração na**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) secção Estúdio Visual do *Como-a-Fazer: Configurar uma função para o tratamento de artigos de dados.*

### <a name="step-2-add-a-new-function"></a>Passo 2: Adicionar uma nova função

Criar uma nova função Azure chamada *ProcessDTUpdatetoTSI.cs* atualizar eventos de telemetria do dispositivo para o Time Series Insights. O tipo de função será **o gatilho do Centro de Eventos.**

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Screenshot do Visual Studio para criar uma nova função Azure do gatilho do centro de eventos tipo.":::

### <a name="step-3-fill-in-function-code"></a>Passo 3: Preencha o código de função

Adicione os seguintes pacotes ao seu projeto:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Substitua o código no ficheiro *ProcessDTUpdatetoTSI.cs* pelo seguinte código:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Guarde o seu código de função.

### <a name="step-4-publish-the-function-app-to-azure"></a>Passo 4: Publicar a app de função para a Azure

Publique o projeto com o *ProcessDTUpdatetoTSI.cs* função para uma aplicação de função em Azure.

Para obter instruções sobre como fazê-lo, consulte a secção [**Publicar a aplicação de função para Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) do *How-to: Configurar uma função para o tratamento do artigo de dados.*

Guarde o nome da aplicação de função para usar mais tarde para configurar as definições de aplicações para os dois centros de eventos.

### <a name="step-5-security-access-for-the-function-app"></a>Passo 5: Acesso à segurança para a aplicação de função

Em seguida, **atribua uma função** de acesso para a função e **configufique as definições da aplicação** para que possa aceder à sua instância Azure Digital Twins. Para obter instruções sobre como fazê-lo, consulte a secção [**Configurar o acesso de segurança para a aplicação**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) de função do *Como-a-: Configurar uma função para o tratamento do artigo de dados.*

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Passo 6: Configurar configurações de aplicativos para os dois centros de eventos

Em seguida, irá adicionar variáveis ambientais nas definições da aplicação de função que lhe permitem aceder ao hub gémeos e ao centro da série de tempo.

Utilize o valor primário do twins **HubConnectionString** que guardou anteriormente para criar uma definição de aplicação na sua aplicação de função que contenha a cadeia de ligação do hub twins:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Utilize o valor primário do hub da série de **tempoConnectionString** que guardou anteriormente para criar uma definição de aplicação na sua aplicação de função que contenha a cadeia de ligação do hub da série de tempo:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e ligar uma instância do Time Series Insights

Nesta secção, irá configurar a instância Time Series Insights para receber dados do seu hub da série de tempo. Para obter mais detalhes sobre este processo, consulte [*Tutorial: Crie um ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorial-set-up-environment.md). Siga os passos abaixo para criar um ambiente de insights de séries temporítros.

1. No [portal Azure,](https://portal.azure.com)procure *ambientes de Insights de Séries Tempotadas* e selecione o botão **Adicionar.** Escolha as seguintes opções para criar o ambiente da série de tempo.

    * **Subscrição** - Escolha a sua subscrição.
        - **Grupo de recursos** - Escolha o seu grupo de recursos.
    * **Nome do ambiente** - Especifique um nome para o seu ambiente de séries temporênciais.
    * **Localização** - Escolha uma localização.
    * **Tier** - Escolha o nível de preços **da Gen2(L1).**
    * **Nome da propriedade** - Introduza **$dtId** (Leia mais sobre a seleção de um valor de ID nas [*melhores práticas para escolher um ID da Série De Tempo).*](../time-series-insights/how-to-select-tsid.md)
    * **Nome da conta de armazenamento** - Especifique o nome da conta de armazenamento.
    * **Ativar a loja quente** - Deixe este campo definido para *Sim*.

    Pode deixar valores predefinidos para outras propriedades nesta página. Selecione o **seguinte : >de origem do evento.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Screenshot do portal Azure para criar o ambiente Time Series Insights. Selecione a sua subscrição, grupo de recursos e localização a partir das respetivas reduções e escolha um nome para o seu ambiente." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Screenshot do portal Azure para criar o ambiente Time Series Insights. O nível de preços da Gen2(L1) é selecionado e o nome de propriedade da série de tempo é $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. No *separador Fonte* de Eventos, escolha os seguintes campos:

   * **Criar uma fonte de evento?** - Escolha *Sim.*
   * **Tipo de origem** - Escolha *o Centro de Eventos*.
   * **Nome** - Especifique um nome para a sua fonte de evento.
   * **Subscrição** - Escolha a sua subscrição Azure.
   * **Event Hub namespace** - Escolha o espaço de nome que criou anteriormente neste artigo.
   * **Nome do Event Hub** - Escolha o nome **do hub da série de tempo** que criou anteriormente neste artigo.
   * **Nome da política de acesso do Event Hub** - Escolha a regra de *auth da série de tempo* que criou anteriormente neste artigo.
   * **Event Hub consumer group** - Selecione *New* e especifique um nome para o seu grupo de consumidores de centros de eventos. Em seguida, *selecione Adicionar*.
   * **Nome da propriedade** - Deixe este campo em branco.
    
    Escolha o botão **'Rever + Criar'** para rever todos os detalhes. Em seguida, selecione o botão **'Rever + Criar'** novamente para criar o ambiente da série-tempo.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Screenshot do portal Azure para criar o ambiente Time Series Insights. Você está criando uma fonte de evento com a informação do centro de eventos de cima. Está também a criar um novo grupo de consumidores." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Enviar dados de IoT para Azure Digital Twins

Para começar a enviar dados para a Time Series Insights, terá de começar a atualizar as propriedades gémeas digitais em Azure Digital Twins com a alteração dos valores de dados.

Utilize o seguinte comando CLI para atualizar a propriedade *Temperatura* no twin *termostato67* que adicionou à sua instância na secção [Pré-Requisitos.](#prerequisites)

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Repita o comando pelo menos mais 4 vezes com valores de temperatura diferentes,** para criar vários pontos de dados que podem ser observados mais tarde no ambiente Time Series Insights.

> [!TIP]
> Se quiser completar este artigo com dados simulados ao vivo em vez de atualizar manualmente os valores gémeos digitais, certifique-se primeiro de que completou a DICA a partir da secção [*Pré-Requisitos*](#prerequisites) para configurar uma função Azure que atualiza gémeos de um dispositivo simulado.
Depois disso, pode executar o dispositivo agora para começar a enviar dados simulados e atualizar o seu gémeo digital através desse fluxo de dados.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizar dados no Time Series Insights

Agora, os dados devem estar a fluir para o seu exemplo de Time Series Insights, pronto para ser analisado. Siga os passos abaixo para explorar os dados que estão a chegar.

1. No [portal Azure,](https://portal.azure.com)procure o nome ambiente da série-tempo que criou anteriormente. Nas opções de menu à esquerda, selecione *Overview* para ver o *URL do Explorador de Insights da Série De Tempo*. Selecione o URL para visualizar as alterações de temperatura refletidas no ambiente Time Series Insights.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Screenshot do portal Azure para selecionar o URL do explorador de Insights de Séries Tempotadas no separador de visão geral do seu ambiente de Insights de Séries Tempotadas." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. No explorador, você verá os gémeos na instância Azure Digital Twins mostrada à esquerda. Selecione *o termóstato67* twin, escolha a *propriedade Temperatura*, e bata Em **Adicionar**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Screenshot do explorador Time Series Insights para selecionar o termostato67, selecione a temperatura da propriedade e bata no add." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Deve agora ver as leituras iniciais da temperatura do termóstato, como mostra abaixo. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Screenshot do explorador TSI para visualizar os dados iniciais da temperatura. É uma linha de valores aleatórios entre 68 e 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Se permitir que uma simulação seja executada por muito mais tempo, a sua visualização será algo parecido com isto:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Screenshot do explorador de TSI onde os dados de temperatura para cada gémeo são grafados em três linhas paralelas de cores diferentes." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais são armazenados por padrão como uma hierarquia plana em Time Series Insights, mas podem ser enriquecidos com informação de modelo e uma hierarquia multi-nível para organização. Para saber mais sobre este processo, leia: 

* [*Tutorial: Definir e aplicar um modelo*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte as seguintes referências:

* [*Como fazer: Gerir um gémeo digital*](./how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](./how-to-query-graph.md)