---
title: Gerir pontos finais e rotas (APIs e CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar e gerir pontos finais e rotas de eventos para os dados da Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b0e440f9fe0b7ce1591318362ac0419b9aa01baf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433306"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gerir pontos finais e rotas em Azure Digital Twins (APIs e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Em Azure Digital Twins, você pode encaminhar notificações de eventos para serviços a [jusante](how-to-interpret-event-data.md) ou recursos de computação conectados. Isto é feito ao, em primeiro lugar, configurar **pontos finais** que podem receber os eventos. Em seguida, pode criar  [**rotas de eventos**](concepts-route-events.md) que especifiquem quais os eventos gerados pela Azure Digital Twins que são entregues a que pontos finais.

Este artigo acompanha-o através do processo de criação de pontos finais e rotas com as [APIs REST,](/rest/api/azure-digitaltwins/)a [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client)e o [Azure Digital Twins CLI](how-to-use-cli.md).

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

Esta secção explica como criar estes pontos finais utilizando o Azure CLI. Também pode gerir pontos finais com o [avião de controlo DigitalTwinsEndpoint APIs](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Criar o ponto final

Uma vez criados os recursos do ponto final, podes usá-los para um ponto final da Azure Digital Twins. Os exemplos a seguir mostram como criar pontos finais utilizando o [ponto final az dt criar](/cli/azure/ext/azure-iot/dt/endpoint/create) comando para o [CLI das Gémeas Digitais Azure](how-to-use-cli.md). Substitua os espaços reservados nos comandos com os detalhes dos seus próprios recursos.

Para criar um ponto final de Grade de Eventos:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Para criar um ponto final do Event Hubs (autenticação baseada em chaves):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Para criar um ponto final de ônibus de serviço (autenticação baseada em chaves):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Depois de executar com sucesso estes comandos, a grelha de eventos, o centro de eventos ou o tópico Service Bus estarão disponíveis como ponto final dentro da Azure Digital Twins, sob o nome que forneceu com o `--endpoint-name` argumento. Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo.](#create-an-event-route)

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Criar um ponto final com autenticação baseada na identidade

Também pode criar um ponto final que tenha autenticação baseada na identidade, para utilizar o ponto final com uma [identidade gerida.](concepts-security.md#managed-identity-for-accessing-other-resources-preview) Esta opção só está disponível para pontos finais do tipo Event Hub e Service Bus (não é suportado para a Grelha de Eventos).

O comando CLI para criar este tipo de ponto final está abaixo. Necessitará dos seguintes valores para ligar aos espaços reservados no comando:
* o ID de recurso Azure da sua instância Azure Digital Twins
* um nome de ponto final
* um tipo de ponto final
* o espaço de nome do recurso endpoint
* o nome do centro de eventos ou tópico de Service Bus
* a localização da sua instância Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Criar um ponto final com letras mortas

Quando um ponto final não consegue entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um certo número de vezes, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como **letra morta.**

Os pontos finais com letras mortas ativadas podem ser configurados com o [CLI](how-to-use-cli.md) das Gémeas Digitais Azure ou [com as APIs do avião de controlo](how-to-use-apis-sdks.md#overview-control-plane-apis).

Para saber mais sobre letras [*mortas, consulte Conceitos: Rotas do evento.*](concepts-route-events.md#dead-letter-events) Para instruções sobre como configurar um ponto final com letras mortas, continue através do resto desta secção.

#### <a name="set-up-storage-resources"></a>Criar recursos de armazenamento

Antes de definir o local da letra morta, deve ter uma [conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal) com um [recipiente](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurado na sua conta Azure. 

Você fornecerá o URI para este recipiente ao criar o ponto final mais tarde. O local da carta morta será fornecido ao ponto final como um contentor URI com um [token SAS](../storage/common/storage-sas-overview.md). Esse símbolo precisa de `write` permissão para o contentor de destino dentro da conta de armazenamento. A letra morta totalmente formada **SAS URI** estará no formato de: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

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
    
#### <a name="create-the-dead-letter-endpoint"></a>Criar o ponto final da letra morta

Para criar um ponto final que tenha a letra ativada, adicione o seguinte parâmetro de letra morta ao [ponto final az dt criar](/cli/azure/ext/azure-iot/dt/endpoint/create) comando para o [CLI das Gémeas Digitais Azure](how-to-use-cli.md).

O valor para o parâmetro é a **letra morta SAS URI** composta pelo nome da conta de armazenamento, nome do recipiente e sinal SAS que recolheu na [secção anterior.](#set-up-storage-resources) Este parâmetro cria o ponto final com a autenticação baseada em teclas.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Adicione este parâmetro ao fim dos comandos de criação de ponto final a partir da secção [*'Criar o ponto final'*](#create-the-endpoint) anteriormente para criar um ponto final do tipo pretendido que tenha a letra morta ativada.

Em alternativa, pode criar pontos finais de cartas mortas utilizando o [avião de controlo Azure Digital Twins APIs](how-to-use-apis-sdks.md#overview-control-plane-apis) em vez do CLI. Para isso, consulte a [documentação digitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para ver como estruturar o pedido e adicionar os parâmetros da letra morta.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Criar um ponto final com autenticação baseada na identidade

Também pode criar um ponto final de letras mortas que tenha autenticação baseada na identidade, para utilizar o ponto final com uma [identidade gerida.](concepts-security.md#managed-identity-for-accessing-other-resources-preview) Esta opção só está disponível para pontos finais do tipo Event Hub e Service Bus (não é suportado para a Grelha de Eventos).

Para criar este tipo de ponto final, utilize o mesmo comando CLI de antes para [criar um ponto final com autenticação baseada na identidade,](#create-an-endpoint-with-identity-based-authentication)com um campo extra na carga útil JSON para um `deadLetterUri` .

Aqui estão os valores que precisará de ligar aos espaços reservados no comando:
* o ID de recurso Azure da sua instância Azure Digital Twins
* um nome de ponto final
* um tipo de ponto final
* o espaço de nome do recurso endpoint
* o nome do centro de eventos ou tópico de Service Bus
* **detalhes da letra morta SAS URI:** nome da conta de armazenamento, nome do recipiente
* a localização da sua instância Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Esquema de armazenamento de mensagens

Uma vez configurado o ponto final com letras mortas, as mensagens com letras mortas serão armazenadas no seguinte formato na sua conta de armazenamento:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

As mensagens com letras mortas corresponderão ao esquema do evento original que se destinava a ser entregue no seu ponto final original.

Aqui está um exemplo de uma mensagem de letra morta para uma [notificação de criação de gémeos:](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
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

**Pré-requisito**: É necessário criar pontos finais, conforme descrito anteriormente neste artigo, antes de poder passar a criar uma rota. Pode proceder à criação de uma rota de eventos assim que os seus pontos finais terminarem a sua configuração.

> [!NOTE]
> Se implementou recentemente os seus pontos finais, valide que terminaram de ser implementados **antes** de tentar usá-los para uma nova rota de eventos. Se a implementação da rota falhar porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se estiver a escrever este fluxo, é melhor prestar contas por isso construindo em 2-3 minutos de tempo de espera para o serviço de ponto final terminar a implantação antes de seguir para a configuração da rota.

Para enviar dados da Azure Digital Twins para um ponto final, terá de definir uma rota de **eventos.** As rotas do evento são usadas para ligar o fluxo de eventos, em todo o sistema e para os serviços a jusante.

Uma definição de rota pode conter estes elementos:
* O nome da rota que pretende usar
* O nome do ponto final que quer usar
* Um filtro que define que eventos são enviados para o ponto final 

Se não houver nome de rota, nenhuma mensagem é encaminhada para fora da Azure Digital Twins. Se houver um nome de rota e o filtro estiver `true` , todas as mensagens são encaminhadas para o ponto final. Se houver um nome de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir a seleção de várias notificações e tipos de eventos. 

As rotas de eventos podem ser criadas com os comandos apis do plano de dados Azure Digital Twins [ **EventRoutes**](/rest/api/digital-twins/dataplane/eventroutes) ou [com os comandos CLI **da rota az dt**](/cli/azure/ext/azure-iot/dt/route). O resto desta secção percorre o processo de criação.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Criar rotas com as APIs e C# SDK

Uma forma de definir rotas de eventos é com as APIs do [plano de dados.](how-to-use-apis-sdks.md#overview-data-plane-apis) As amostras desta secção utilizam o [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client).

`CreateOrReplaceEventRouteAsync` é a chamada SDK que é usada para adicionar uma rota de evento. Aqui está um exemplo da sua utilização:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

#### <a name="event-route-sample-sdk-code"></a>Código SDK de amostra de rota de evento

O seguinte método de amostra mostra como criar, listar e eliminar uma rota de eventos com o C# SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Criar rotas com o CLI

As rotas também podem ser geridas usando os comandos [de rota az dt](/cli/azure/ext/azure-iot/dt/route) para o CLI das Gémeas Digitais Azure. 

Para obter mais informações sobre a utilização do CLI e quais os comandos disponíveis, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md).

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

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passos seguintes

Leia sobre os diferentes tipos de mensagens de evento que pode receber:
* [*Como fazer: Interpretar dados do evento*](how-to-interpret-event-data.md)
