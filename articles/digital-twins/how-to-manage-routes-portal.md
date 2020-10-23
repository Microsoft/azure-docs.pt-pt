---
title: Gerir pontos finais e rotas (portal)
titleSuffix: Azure Digital Twins
description: Veja como configurar e gerir pontos finais e rotas de eventos para dados da Azure Digital Twins utilizando o portal Azure.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a2164afef3807a345819a18274eb01f2116de9fd
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427951"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gerir pontos finais e rotas em Azure Digital Twins (portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Em Azure Digital Twins, você pode encaminhar notificações de eventos para serviços a [jusante](how-to-interpret-event-data.md) ou recursos de computação conectados. Isto é feito primeiro através da criação **de pontos finais** que podem receber os eventos. Em seguida, pode criar [**rotas de eventos**](concepts-route-events.md) que especifiquem quais os eventos gerados pela Azure Digital Twins que são entregues a que pontos finais.

Este artigo acompanha-o através do processo de criação de pontos finais e rotas utilizando o [portal Azure.](https://portal.azure.com)

Também pode gerir pontos finais e rotas com os [ApIs eventRoutes,](how-to-use-apis-sdks.md)o [.NET (C#) SDK,](https://www.nuget.org/packages/Azure.DigitalTwins.Core)ou o [Azure Digital Twins CLI](how-to-use-cli.md). Para uma versão deste artigo que utiliza estes mecanismos em vez do portal, consulte [*Como-a-fazer: Gerir pontos finais e rotas (APIs e CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

* Você vai precisar de uma **conta Azure** (você pode configurar uma [gratuitamente aqui)](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Você precisará de uma **instância Azure Digital Twins** na sua assinatura Azure. Se ainda não tiver um caso, pode criar um utilizando os passos em [*Como-a-: Configurar um caso e autenticação*](how-to-set-up-instance-portal.md). Ter os seguintes valores da configuração útil para usar mais tarde neste artigo:
    - Nome da instância
    - Grupo de recursos

Pode encontrar estes detalhes no [portal Azure](https://portal.azure.com) depois de configurar o seu caso. Inicie sessão no portal e procure o nome do seu caso na barra de pesquisa do portal.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::

Selecione a sua instância a partir dos resultados para ver a página de detalhes para o seu exemplo:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Screenshot da barra de pesquisa do portal Azure." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto final para a Azure Digital Twins

Estes são os tipos suportados de pontos finais que pode criar, para o seu exemplo:
* [Event Grid](../event-grid/overview.md) 
* [Hubs de Eventos](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de pontos [*finais, consulte Escolha entre os serviços de mensagens Azure.*](../event-grid/compare-messaging-services.md)

Para ligar um ponto final à Azure Digital Twins, o tópico da grelha de eventos, o centro de eventos ou o Service Bus que está a usar para o ponto final já precisa de existir. 

### <a name="create-an-event-grid-endpoint"></a>Criar um ponto final de Grade de Eventos

**Pré-requisito**: Crie um tópico de grelha de eventos seguindo os passos na [secção Criar um tópico *personalizado* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) dos eventos Event Grid *Custom eventos* quickstart.

Uma vez criado o tópico, pode ligá-lo à Azure Digital Twins a partir da página da sua instância Azure Digital Twins no [portal Azure](https://portal.azure.com) (pode encontrar a instância introduzindo o seu nome na barra de pesquisa do portal).

A partir do menu de exemplos, selecione _Endpoints_. Em seguida, a partir da página *Endpoints* que se segue, selecione *+ Crie um ponto final*. 

Na página *'Criar'* que se abre, pode criar um ponto final de grelha de _evento tipo,_ selecionando o botão de rádio correspondente. Complete os outros detalhes: insira um nome para o seu ponto final no campo _Nome,_ escolha a sua _Subscrição_ a partir do dropdown e escolha o tópico de grelha de  _evento_ pré-criado a partir do terceiro dropdown.

Em seguida, crie o seu ponto final batendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::

Pode verificar se o ponto final foi criado com sucesso, verificando o ícone de notificação na barra de portal Azure superior: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Screenshot da barra de pesquisa do portal Azure." border="false":::

Também pode ver o ponto final que foi criado de volta na página *Endpoints* para a sua instância Azure Digital Twins.

Se a criação do ponto final falhar, observe a mensagem de erro e redaça após alguns minutos.

Agora, o tópico da grelha de eventos está disponível como ponto final dentro de Azure Digital Twins, sob o nome especificado no campo _Nome._ Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo.](#create-an-event-route)

### <a name="create-an-event-hubs-endpoint"></a>Criar um ponto final de Centro de Eventos

**Pré-requisitos:** 
* Você precisará de um espaço de _nomes de Event Hubs_ e um _centro de eventos._ Crie ambos seguindo os passos nos Centros de Eventos Crie um centro de [*eventos*](../event-hubs/event-hubs-create.md) de arranque rápido.
* Vai precisar de uma _regra de autorização._ Para criar isto, consulte os Centros de Eventos autorizando o acesso aos recursos do Event Hubs utilizando o artigo [*Assinaturas de Acesso Partilhado.*](../event-hubs/authorize-access-shared-access-signature.md)

Aceda à página de detalhes para a sua instância Azure Digital Twins no [portal Azure](https://portal.azure.com) (pode encontrá-lo introduzindo o seu nome na barra de pesquisa do portal).

A partir do menu de exemplos, selecione _Endpoints_. Em seguida, a partir da página *Endpoints* que se segue, selecione *+ Crie um ponto final*. 

Na página *'Criar'* que se abre, pode criar um ponto final do Tipo _Event Hub_ selecionando o botão de rádio correspondente. Insira um nome para o seu ponto final no campo _Nome._ Em seguida, selecione a sua _Subscrição_, e o seu espaço de _nome de hub de evento_pré-criado, Centro de _Eventos_e Regra de _Autorização_ a partir das respetivas reduções.

Em seguida, crie o seu ponto final batendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::

Pode verificar se o ponto final foi criado com sucesso, verificando o ícone de notificação na barra de portal Azure superior. 

Se a criação do ponto final falhar, observe a mensagem de erro e redaça após alguns minutos.

Agora, o hub do Evento está disponível como ponto final dentro das Gémeas Digitais Azure, sob o nome especificado no campo _Nome._ Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo.](#create-an-event-route)

### <a name="create-a-service-bus-endpoint"></a>Criar um ponto final de ônibus de serviço

**Pré-requisitos:** 
* Você precisará de um _espaço de nome de service bus_ e um tópico de service _bus_. Crie ambos seguindo os passos no Service Bus [*Criar tópicos e subscrições*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) de forma rápida. Não é necessário completar as [*subscrições criar para a*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) secção de tópicos.
* Vai precisar de uma _regra de autorização._ Para criar isto, consulte o artigo [*de Autenticação e Autorização*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) do Autocarro de Serviço.

Aceda à página de detalhes para a sua instância Azure Digital Twins no [portal Azure](https://portal.azure.com) (pode encontrá-lo introduzindo o seu nome na barra de pesquisa do portal).

A partir do menu de exemplos, selecione _Endpoints_. Em seguida, a partir da página *Endpoints* que se segue, selecione *+ Crie um ponto final*. 

Na página *'Criar'* que se abre, pode criar um ponto final do tipo _Service Bus_ selecionando o botão de rádio correspondente. Insira um nome para o seu ponto final no campo _Nome._ Em seguida, selecione a sua _Subscrição_, e o seu espaço de _nomes_de Service Bus pré-criado, _tópico de ônibus_de serviço e regra de _autorização_ a partir das respetivas reduções.

Em seguida, crie o seu ponto final batendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::

Pode verificar se o ponto final foi criado com sucesso, verificando o ícone de notificação na barra de portal Azure superior. 

Se a criação do ponto final falhar, observe a mensagem de erro e redaça após alguns minutos.

Agora, o tópico do Service Bus está disponível como ponto final dentro da Azure Digital Twins, sob o nome especificado no campo _Nome._ Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo.](#create-an-event-route)

### <a name="create-an-endpoint-with-dead-lettering"></a>Criar um ponto final com letras mortas

Quando um ponto final não consegue entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um certo número de vezes, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como **letra morta.**

Para criar um ponto final com letras mortas ativadas, deve utilizar as [APIs ARM](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para criar o seu ponto final, em vez do portal Azure.

Para obter instruções sobre como fazê-lo com as APIs, consulte as [*APIs e a*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) versão CLI deste artigo.

## <a name="create-an-event-route"></a>Criar uma rota de eventos

Para enviar dados da Azure Digital Twins para um ponto final, terá de definir uma rota de **eventos.** Estas rotas permitem que os desenvolvedores liguem o fluxo de eventos, em todo o sistema e para os serviços a jusante. Leia mais sobre as rotas de eventos em [*Conceitos: Eventos de Roteamento Azure Digital Twins*](concepts-route-events.md).

**Pré-requisito**: É necessário criar pontos finais, conforme descrito anteriormente neste artigo, antes de poder passar a criar uma rota. Pode proceder à criação de uma rota de eventos assim que os seus pontos finais terminarem a sua configuração.

>[!NOTE]
>Se implementou recentemente os seus pontos finais, valide que terminaram de ser implementados **antes** de tentar usá-los para uma nova rota de eventos. Se não conseguir configurar a rota porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.

### <a name="creation-steps-with-the-azure-portal"></a>Etapas de criação com o portal Azure

Uma definição de rota de evento contém estes elementos:
* O nome da rota que pretende usar
* O nome do ponto final que quer usar
* Um filtro que define quais os eventos enviados para o ponto final
    - Para desativar a rota para que não sejam enviados eventos, use um valor de filtro de `false`
    - Para ativar uma rota que não tenha filtragem específica, use um valor de filtro de `true`
    - Para obter mais informações sobre qualquer outro tipo de filtro, consulte a secção [*de eventos do Filtro*](#filter-events) abaixo.

Uma única rota pode permitir a seleção de várias notificações e tipos de eventos.

Para criar uma rota de eventos, aceda à página de detalhes para a sua instância Azure Digital Twins no [portal Azure](https://portal.azure.com) (pode encontrar a instância introduzindo o seu nome na barra de pesquisa do portal).

A partir do menu de instâncias, selecione _rotas de Eventos._ Em seguida, a partir da página *de rotas do Evento* que se segue, selecione + Crie uma rota de *eventos.* 

Na página de rota de *eventos* Create que se abre, escolha no mínimo:
* Um nome para o seu percurso no campo _Nome_
* O _Ponto Final_ que gostaria de usar para criar a rota 

Para que o percurso seja ativado, também deve adicionar um filtro de rota de **eventos** de pelo menos `true` . (Deixar o valor padrão de `false` irá criar a rota, mas nenhum evento será enviado para ele.) Para isso, alternar o interruptor para o _editor Avançado_ para o ativar e escrever na `true` caixa *Filtro.*

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Screenshot da barra de pesquisa do portal Azure." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Quando terminar, premir o botão _Guardar_ para criar a sua rota de evento.

## <a name="filter-events"></a>Filtrar eventos

Como descrito acima, as rotas têm um campo **de filtro.** Se o valor do filtro na sua rota `false` for, não serão enviados eventos para o seu ponto final. 

Depois de permitir o filtro mínimo `true` de, os pontos finais receberão uma variedade de eventos da Azure Digital Twins:
* Telemetria disparada por [gémeos digitais](concepts-twins-graph.md) usando o serviço Azure Digital Twins API
* Notificações de mudança de propriedade gémea, disparadas sobre alterações de propriedade para qualquer gémeo no caso Azure Digital Twins
* Eventos de ciclo de vida, disparados quando gémeos ou relacionamentos são criados ou eliminados

Pode restringir os tipos de eventos que são enviados definindo um filtro mais específico.

Para adicionar um filtro de eventos enquanto está a criar uma rota de eventos, utilize a secção de filtro de rota de _eventos_ da página Criar uma página de *rota de evento.* 

Pode selecionar a partir de algumas opções básicas de filtro comum ou utilizar as opções avançadas de filtro para escrever os seus próprios filtros personalizados.

#### <a name="use-the-basic-filters"></a>Use os filtros básicos

Para utilizar os filtros básicos, expanda a opção _de tipos de Eventos_ e selecione as caixas de verificação correspondentes aos eventos que pretende enviar para o seu ponto final. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Isto irá preencher automaticamente a caixa de texto do filtro com o texto do filtro selecionado:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Use os filtros avançados

Em alternativa, pode utilizar a opção de filtro avançado para escrever os seus próprios filtros personalizados.

Para criar uma rota de eventos com opções avançadas de filtro, altere o interruptor para o _editor Avançado_ para o ativar. Em seguida, pode escrever os seus próprios filtros de eventos na caixa *filtro:*

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Screenshot da barra de pesquisa do portal Azure.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aqui estão os filtros de rota suportados. O detalhe na coluna *de esquema de texto do filtro* é o texto que pode ser introduzido na caixa do filtro.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passos seguintes

Leia sobre os diferentes tipos de mensagens de evento que pode receber:
* [*Como fazer: Interpretar dados do evento*](how-to-interpret-event-data.md)
