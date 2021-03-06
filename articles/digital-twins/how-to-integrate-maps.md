---
title: Integrar com o Azure Maps
titleSuffix: Azure Digital Twins
description: Veja como usar as Funções Azure para criar uma função que pode usar o gráfico gémeo e as notificações de Azure Digital Twins para atualizar um mapa interior do Azure Maps.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201435"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Use gémeos digitais Azure para atualizar um mapa interior do Azure Maps

Este artigo percorre os passos necessários para utilizar os dados da Azure Digital Twins para atualizar as informações exibidas num *mapa interior* utilizando [o Azure Maps](../azure-maps/about-azure-maps.md). A Azure Digital Twins armazena um gráfico das suas relações com dispositivos IoT e encaminha a telemetria para diferentes pontos finais, tornando-se o serviço perfeito para atualizar sobreposições informativas nos mapas.

Este como-fazer cobrirá:

1. Configurar a sua instância Azure Digital Twins para enviar eventos de atualização dupla para uma função em [Funções Azure](../azure-functions/functions-overview.md).
2. Criar uma função para atualizar um Azure Maps mapas interiores recurso stateet.
3. Como armazenar o ID dos seus mapas e apresentar iD de estado no gráfico Azure Digital Twins.

### <a name="prerequisites"></a>Pré-requisitos

* Siga o Tutorial de Gémeos Digitais Azure: [*Ligue uma solução de ponta a ponta*](./tutorial-end-to-end.md).
    * Vais estender este gémeo com um ponto final adicional e uma rota. Também irá adicionar outra função à sua aplicação de função a partir desse tutorial. 
* Siga o Tutorial Azure [*Maps: Use O Criador de Mapas Azure para criar mapas interiores*](../azure-maps/tutorial-creator-indoor-maps.md) para criar um mapa interior Azure Maps com um *estado de recurso.*
    * [Os estados de recurso](../azure-maps/creator-indoor-maps.md#feature-statesets) são coleções de propriedades dinâmicas (estados) atribuídas a funcionalidades de conjunto de dados, tais como salas ou equipamentos. No tutorial do Azure Maps acima, a funcionalidade stateet armazena o estado da sala que você vai exibir num mapa.
    * Você precisará da sua chave de *subscrição* *de stateset id* e Azure Maps.

### <a name="topology"></a>Topologia

A imagem abaixo ilustra onde os elementos de integração de mapas interiores neste tutorial se encaixam num cenário maior e extremo de Gémeos Digitais Azure.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Uma visão dos serviços da Azure num cenário de ponta a ponta, destacando a peça de Integração de Mapas Interiores" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Criar uma função para atualizar um mapa quando os gémeos atualizarem

Primeiro, você vai criar uma rota em Azure Digital Twins para encaminhar todos os eventos de atualização de dois para um tópico de grelha de eventos. Em seguida, utilizará uma função para ler as mensagens de atualização e atualizar um estado de funcionalidade no Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e filtrar notificações de atualização de duplos digitais

As instâncias Azure Digital Twins podem emitir eventos de atualização dupla sempre que o estado de um gémeo é atualizado. O Tutorial de Gémeos Digitais Azure: [*Ligue uma solução de ponta a ponta*](./tutorial-end-to-end.md) ligada acima, percorre um cenário em que um termómetro é usado para atualizar um atributo de temperatura ligado ao gémeo de uma sala. Irá estender essa solução subscrevendo notificações para gémeos e usando essa informação para atualizar os seus mapas.

Este padrão lê-se diretamente do twin da sala, em vez do dispositivo IoT, o que lhe dá flexibilidade para alterar a fonte de dados subjacente à temperatura sem precisar de atualizar a sua lógica de mapeamento. Por exemplo, pode adicionar vários termómetros ou definir esta sala para partilhar um termómetro com outra sala, tudo sem necessidade de atualizar a lógica do mapa.

1. Crie um tópico de grelha de eventos, que receberá eventos da sua instância Azure Digital Twins.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Crie um ponto final para ligar o tópico da grelha de eventos à Azure Digital Twins.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Crie uma rota  no Azure Digital Twins para enviar eventos de atualização de duplos digitais para o ponto final.

    >[!NOTE]
    >Atualmente, existe um **problema conhecido** no Cloud Shell que está a afetar os seguintes grupos de comando: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Para resolver, execute `az login` no Cloud Shell antes de executar o comando ou utilize a [CLI local](/cli/azure/install-azure-cli) em vez do Cloud Shell. Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Criar uma função para atualizar mapas

Vai criar uma **função desencadeada por Event Grid** dentro da sua aplicação de função a partir do tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta).*](./tutorial-end-to-end.md) Esta função irá desembalar essas notificações e enviar atualizações para um estado de funcionalidade do Azure Maps para atualizar a temperatura de um quarto.

Consulte o seguinte documento para obter informações de referência: [*Azure Event Grid trigger for Azure Functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Substitua o código de função pelo seguinte código. Filtrará apenas atualizações para gémeos espaciais, lerá a temperatura atualizada e enviará essa informação para o Azure Maps.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

Terá de definir duas variáveis ambientais na sua aplicação de função. Uma delas é a sua chave de subscrição primária do [Azure Maps,](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)e uma é o seu [ID de estado Azure Maps.](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Ver atualizações ao vivo no seu mapa

Para ver a temperatura de atualização ao vivo, siga os passos abaixo:

1. Comece a enviar dados IoT simulados executando o projeto **DeviceSimulator** a partir do Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md). As instruções para tal encontram-se no [*Configure e executam a secção de simulação.*](././tutorial-end-to-end.md#configure-and-run-the-simulation)
2. Utilize [o módulo Interior **Azure Maps**](../azure-maps/how-to-use-indoor-module.md) para tornar os seus mapas internos criados no Azure Maps Creator.
    1. Copie o HTML a partir do exemplo: Utilize a secção do [*Módulo mapas interiores*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) do tutorial de mapas [*interiores: Utilize o módulo Azure Maps Indoor Maps para*](../azure-maps/how-to-use-indoor-module.md) um ficheiro local.
    1. Substitua a *tecla de subscrição*, *o tilesetId* e *o statesetID*  no ficheiro HTML local pelos seus valores.
    1. Abra o ficheiro no seu navegador.

Ambas as amostras enviam temperatura numa gama compatível, por isso deve ver a cor da atualização do quarto 121 no mapa a cada 30 segundos.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Um mapa de escritório mostrando a sala 121 cor de laranja":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Guarde as informações dos seus mapas em Azure Digital Twins

Agora que tem uma solução codificada para atualizar as informações dos seus mapas, pode utilizar o gráfico Azure Digital Twins para armazenar todas as informações necessárias para atualizar o seu mapa interno. Isto incluiria o ID do estado, o ID de subscrição de mapas e o ID de cada mapa e localização, respectivamente. 

Uma solução para este exemplo específico implicaria atualizar cada espaço de nível superior para ter um iD de estado e mapear o iD de subscrição, e atualizar cada quarto para ter um ID de funcionalidade. Basta definir estes valores uma vez ao rubricar o gráfico gémeo e, em seguida, consultar esses valores para cada evento de atualização dupla.

Dependendo da configuração da sua topologia, poderá armazenar estes três atributos em diferentes níveis correlacionados com a granularidade do seu mapa.

## <a name="next-steps"></a>Passos seguintes

Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte as seguintes referências:

* [*Como fazer: Gerir gémeos digitais*](./how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](./how-to-query-graph.md)
