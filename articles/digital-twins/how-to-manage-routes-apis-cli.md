---
title: Gerir pontos finais e rotas (APIs e CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar e gerir pontos finais e rotas de eventos para os dados da Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fa699163fdf445624c918e714fda890a41a67f07
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682652"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gerir pontos finais e rotas em Azure Digital Twins (APIs e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Em Azure Digital Twins, você pode encaminhar notificações de eventos para serviços a [jusante](how-to-interpret-event-data.md) ou recursos de computação conectados. Isto é feito ao, em primeiro lugar, configurar **pontos finais** que podem receber os eventos. Em seguida, pode criar  [**rotas de eventos**](concepts-route-events.md) que especifiquem quais os eventos gerados pela Azure Digital Twins que são entregues a que pontos finais.

Este artigo acompanha-o através do processo de criação de pontos finais e rotas com as [APIs rotas de eventos,](/rest/api/digital-twins/dataplane/eventroutes)o [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)e o [Azure Digital Twins CLI](how-to-use-cli.md).

Em alternativa, também pode gerir pontos finais e rotas com o [portal Azure.](https://portal.azure.com) Para uma versão deste artigo que utiliza o portal, consulte [*Como fazer: Gerir pontos finais e rotas (portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

- Você vai precisar de uma **conta Azure** (você pode configurar uma [gratuitamente aqui)](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Você precisará de uma **instância Azure Digital Twins** na sua assinatura Azure. Se ainda não tiver um caso, pode criar um utilizando os passos em [*Como-a-: Configurar um caso e autenticação*](how-to-set-up-instance-cli.md). Ter os seguintes valores da configuração útil para usar mais tarde neste artigo:
    - Nome da instância
    - Grupo de recursos

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto final para a Azure Digital Twins

Estes são os tipos suportados de pontos finais que pode criar, para o seu exemplo:
* [Event Grid](../event-grid/overview.md)
* [Hubs de Eventos](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de pontos [*finais, consulte Escolha entre os serviços de mensagens Azure.*](../event-grid/compare-messaging-services.md)

Para ligar um ponto final à Azure Digital Twins, o tópico da grelha de eventos, o centro de eventos ou o Service Bus que está a usar para o ponto final já precisa de existir. 

### <a name="create-an-event-grid-endpoint"></a>Criar um ponto final de Grade de Eventos

O exemplo a seguir mostra como criar um ponto final do tipo de grelha de evento usando o Azure CLI.

Primeiro, criar um tópico de grelha de eventos. Pode utilizar o seguinte comando ou ver os passos mais detalhadamente visitando [a secção *de tópicos personalizados*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) dos eventos Event Grid *Custom.*

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Para obter uma lista de nomes da região de Azure que podem ser passados em comandos no CLI Azure, executar este comando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Uma vez criado o tópico, pode ligá-lo à Azure Digital Twins com o seguinte [comando CLI Azure Digital Twins](how-to-use-cli.md):

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Agora, o tópico da grelha de eventos está disponível como ponto final dentro da Azure Digital Twins, sob o nome especificado com o `--endpoint-name` argumento. Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo](#create-an-event-route) usando o serviço Azure Digital Twins API.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Crie um centro de eventos ou ponto final de ônibus de serviço

O processo de criação de Centros de Eventos ou pontos finais de autocarros de serviço é semelhante ao processo de Grelha de Eventos acima mostrado.

Primeiro, crie os seus recursos que usará como ponto final. Aqui está o que é necessário:
* Service Bus: _Service Bus namespace_, _Service Bus topic_, Regra de _autorização_
* Centros de eventos: _Espaço de nomes de Centros de Eventos,_ _centro de eventos,_ regra _de autorização_

Em seguida, utilize os seguintes comandos para criar os pontos finais em Azure Digital Twins: 

* Adicionar ponto final de tópico de ônibus de serviço (requer um recurso de service bus pré-criado)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Adicionar ponto final do Event Hubs (requer recurso de Centros de Eventos pré-criado)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Criar um ponto final com letras mortas

Quando um ponto final não consegue entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um certo número de vezes, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como **letra morta.**

Para saber mais sobre letras [*mortas, consulte Conceitos: Rotas do evento.*](concepts-route-events.md#dead-letter-events) Para instruções sobre como configurar um ponto final com letras mortas, continue através do resto desta secção.

#### <a name="set-up-storage-resources"></a>Criar recursos de armazenamento

Antes de definir o local da letra morta, deve ter uma [conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal) com um [recipiente](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurado na sua conta Azure. 

Você fornecerá o URL para este recipiente ao criar o ponto final mais tarde. O local da carta morta será fornecido ao ponto final como URL de contentor com um [símbolo SAS](../storage/common/storage-sas-overview.md). Esse símbolo precisa de `write` permissão para o contentor de destino dentro da conta de armazenamento. O URL totalmente formado será no formato de: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>` .

Siga os passos abaixo para configurar estes recursos de armazenamento na sua conta Azure, para preparar a configuração da ligação de ponto final na secção seguinte.

1. Siga os passos na [*Criar uma conta de armazenamento*](../storage/common/storage-account-create.md?tabs=azure-portal) para criar uma conta de **armazenamento** na sua subscrição Azure. Tome nota do nome da conta de armazenamento para usá-lo mais tarde.
2. Siga os passos na [*Criar um recipiente*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para criar um **recipiente** dentro da nova conta de armazenamento. Tome nota do nome do recipiente para usá-lo mais tarde.
3. Em seguida, crie um **token SAS para a** sua conta de armazenamento que o ponto final pode usar para aceder ao mesmo. Comece por navegar na sua conta de armazenamento no [portal Azure](https://ms.portal.azure.com/#home) (pode encontrá-la pelo nome com a barra de pesquisa do portal).
4. Na página da conta de armazenamento, escolha o link _de assinatura de acesso Partilhado_ na barra de navegação esquerda para começar a configurar o token SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Página de conta de armazenamento no portal Azure" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Na *página* de assinatura de acesso Partilhado , nos *serviços permitidos* e *tipos de recursos permitidos,* selecione as definições que quiser. Terá de selecionar pelo menos uma caixa em cada categoria. Sob *permissões Permitidas,* escolha **Escrever** (também pode selecionar outras permissões se quiser).
1. Defina os valores que quiser para as definições restantes.
1. Quando terminar, selecione o _botão de cadeia Generate SAS e de ligação_ para gerar o token SAS. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Página de conta de armazenamento no portal Azure mostrando toda a seleção de definição para gerar um token SAS, e realçando o botão 'Gerar SAS e cadeia de conexão'" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Isto irá gerar vários valores de cadeia sas e de ligação na parte inferior da mesma página, por baixo das seleções de definição. Desloque-se para baixo para ver os valores e use o ícone *copy to clipboard* para copiar o valor **simbólico SAS.** Guarde-o para usar mais tarde.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Copiar o símbolo da SAS para usar no segredo da letra morta." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="configure-the-endpoint"></a>Configure o ponto final

Para criar um ponto final que tenha a inscrição ativada, terá de criar o ponto final utilizando as APIs do Gestor de Recursos Azure. 

1. Em primeiro lugar, utilize a [documentação apis do Gestor de Recursos Azure](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para configurar um pedido de criação de um ponto final e preencher os parâmetros de pedido necessários. 

1. Em seguida, adicione um `deadLetterSecret` campo ao objeto de propriedades no **corpo** do pedido. Deite este valor de acordo com o modelo abaixo, que cria um URL a partir do nome da conta de armazenamento, nome do recipiente e valor simbólico SAS que recolheu na [secção anterior](#set-up-storage-resources).
      
  :::code language="json" source="~/digital-twins-docs-samples/api-requests/deadLetterEndpoint.json":::

1. Envie o pedido para criar o ponto final.

Para obter mais informações sobre a estruturação deste pedido, consulte a documentação API do Azure Digital Twins REST: [Endpoints - DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Esquema de armazenamento de mensagens

Uma vez configurado o ponto final com letras mortas, as mensagens com letras mortas serão armazenadas no seguinte formato na sua conta de armazenamento:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

As mensagens com letras mortas corresponderão ao esquema do evento original que se destinava a ser entregue no seu ponto final original.

Aqui está um exemplo de uma mensagem de letra morta para uma [notificação de criação de gémeos:](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Criar uma rota de eventos

Para enviar dados da Azure Digital Twins para um ponto final, terá de definir uma rota de **eventos.** Azure Digital Twins **EventRoutes APIs** permitem aos desenvolvedores ligar o fluxo de eventos, em todo o sistema e para serviços a jusante. Leia mais sobre as rotas de eventos em [*Conceitos: Eventos de Roteamento Azure Digital Twins*](concepts-route-events.md).

As amostras desta secção utilizam o [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

**Pré-requisito**: É necessário criar pontos finais, conforme descrito anteriormente neste artigo, antes de poder passar a criar uma rota. Pode proceder à criação de uma rota de eventos assim que os seus pontos finais terminarem a sua configuração.

> [!NOTE]
> Se implementou recentemente os seus pontos finais, valide que terminaram de ser implementados **antes** de tentar usá-los para uma nova rota de eventos. Se a implementação da rota falhar porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se estiver a escrever este fluxo, é melhor prestar contas por isso construindo em 2-3 minutos de tempo de espera para o serviço de ponto final terminar a implantação antes de seguir para a configuração da rota.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Código de criação com APIs e o C# SDK

As rotas do evento são definidas usando [APIs de plano de dados.](how-to-use-apis-sdks.md#overview-data-plane-apis) 

Uma definição de rota pode conter estes elementos:
* O nome da rota que pretende usar
* O nome do ponto final que quer usar
* Um filtro que define que eventos são enviados para o ponto final 

Se não houver nome de rota, nenhuma mensagem é encaminhada para fora da Azure Digital Twins. Se houver um nome de rota e o filtro estiver `true` , todas as mensagens são encaminhadas para o ponto final. Se houver um nome de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir a seleção de várias notificações e tipos de eventos. 

`CreateOrReplaceEventRouteAsync` é a chamada SDK que é usada para adicionar uma rota de evento. Aqui está um exemplo da sua utilização:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

### <a name="event-route-sample-code"></a>Código de amostra de rota de evento

O seguinte método de amostra mostra como criar, listar e eliminar uma rota de eventos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

## <a name="filter-events"></a>Filtrar eventos

Sem filtragem, os pontos finais recebem uma variedade de eventos da Azure Digital Twins:
* Telemetria disparada por [gémeos digitais](concepts-twins-graph.md) usando o serviço Azure Digital Twins API
* Notificações de mudança de propriedade gémea, disparadas sobre alterações de propriedade para qualquer gémeo no caso Azure Digital Twins
* Eventos de ciclo de vida, disparados quando gémeos ou relacionamentos são criados ou eliminados

Pode restringir os eventos que estão a ser enviados adicionando um **filtro** para um ponto final na sua rota de eventos.

Para adicionar um filtro, pode utilizar um pedido PUT para *https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31* com o seguinte corpo:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Aqui estão os filtros de rota suportados. Utilize o detalhe na coluna *de esquema de texto do filtro* para substituir o espaço reservado no corpo de pedido `<filter-text>` acima.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Gerir pontos finais e rotas com CLI

Os pontos finais e as rotas também podem ser geridos usando o CLI das Gémeas Digitais Azure. Para obter mais informações sobre a utilização do CLI e quais os comandos disponíveis, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passos seguintes

Leia sobre os diferentes tipos de mensagens de evento que pode receber:
* [*Como fazer: Interpretar dados do evento*](how-to-interpret-event-data.md)
