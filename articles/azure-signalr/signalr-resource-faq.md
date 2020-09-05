---
title: O Serviço Azure SignalR faz perguntas frequentemente
description: Obtenha respostas para perguntas frequentes sobre o Serviço Azure SignalR, incluindo resolução de problemas e cenários típicos de utilização.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489566"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço Azure SignalR está pronto para ser utilizado pela produção?

Yes.
Para o anúncio de disponibilidade geral, consulte [o Serviço Azure SignalR agora geralmente disponível](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET o Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) está totalmente suportado.

O suporte para ASP.NET SignalR ainda está em *pré-visualização pública*. [Aqui está um exemplo de código.](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A ligação ao cliente fecha-se com a mensagem de erro "Não há servidor disponível". O que é que isso significa?

Este erro ocorre apenas quando os clientes estão a enviar mensagens para o Serviço Azure SignalR.

Se não tiver qualquer servidor de aplicação e utilizar apenas a API do Serviço Azure SignalR, este comportamento é *por design*.
Na arquitetura sem servidor, as ligações do cliente estão em modo *de escuta* e não enviam nenhuma mensagem para o Serviço Azure SignalR.
Leia [mais sobre a API REST.](./signalr-quickstart-rest-api.md)

Se tiver servidores de aplicações, esta mensagem de erro significa que nenhum servidor de aplicação está ligado à sua instância do Serviço Azure SignalR.

As causas possíveis são:
- Nenhum servidor de aplicações está ligado ao Serviço Azure SignalR. Verifique os registos do servidor de aplicações para obter possíveis erros de ligação. Este caso é raro numa definição de alta disponibilidade que tem mais de um servidor de aplicação.
- Existem problemas de conectividade com as instâncias do Serviço Azure SignalR. Esta questão é transitória e as ocorrências irão automaticamente recuperar.
Se persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou crie um pedido de apoio em [Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando existem vários servidores de aplicações, as mensagens do cliente são enviadas para todos os servidores ou apenas um deles?

Há um mapeamento de um para um entre um cliente e um servidor de aplicações. As mensagens de um cliente são sempre enviadas para o mesmo servidor de aplicações.

O mapeamento é mantido até que o servidor de cliente ou aplicação se desligue.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicações está em baixo, como posso encontrá-lo e ser notificado?

O Serviço Azure SignalR monitoriza os batimentos cardíacos dos servidores de aplicações.
Se os batimentos cardíacos não forem recebidos por um determinado período de tempo, o servidor de aplicação é considerado offline. Todas as ligações do cliente mapeadas a este servidor de aplicações serão desligadas.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Porque é que o meu costume `IUserIdProvider` é uma exceção quando estou a mudar de ASP.NET Core SignalR SDK para Azure SignalR Service SDK?

O parâmetro `HubConnectionContext context` é diferente entre o ASP.NET Core SignalR SDK e o Serviço Azure SignalR SDK quando é `IUserIdProvider` chamado.

Em ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da ligação física do cliente com valores válidos para todas as propriedades.

No Serviço Azure SignalR SDK, `HubConnectionContext context` é o contexto da ligação lógica do cliente. O cliente físico está ligado à instância do Serviço Azure SignalR, pelo que apenas um número limitado de propriedades são fornecidas.

Por enquanto, `HubConnectionContext.GetHttpContext()` `HubConnectionContext.User` apenas estão disponíveis para acesso.
Pode [verificar o código de origem.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço Azure SignalR do lado do servidor com ASP.NET Core SignalR? Por exemplo, posso desativar o transporte WebSocket?

Não.

O Serviço Azure SignalR fornece os três transportes que ASP.NET o Core SignalR suporta por padrão. Não é configurável. O Serviço Azure SignalR tratará de ligações e transportes para todas as ligações do cliente.

Pode configurar os transportes do lado do cliente, conforme documentado na [configuração do Core SignalR ASP.NET](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado de métricas como a contagem de mensagens ou a contagem de ligação mostrada no portal Azure? Que tipo de agregação devo escolher?

Pode encontrar detalhes sobre a cálculo destas métricas em [Mensagens e ligações no Serviço Azure SignalR](signalr-concept-messages-and-connections.md).

No painel geral dos recursos do Serviço Azure SignalR, já escolhemos o tipo de agregação apropriado para si. Se for ao painel de métricas, pode levar o tipo de agregação a [Mensagens e ligações no Serviço Azure SignalR](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) como referência.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual é o significado dos `Default` `Serverless` modos de `Classic` serviço e dos modos de serviço? Como posso escolher?

Aqui está a informação sobre os modos:
* `Default` o modo *requer* um servidor hub. Neste modo, o Serviço Azure SignalR encaminha o tráfego do cliente para as suas ligações de servidores de hub conectados. O Serviço Azure SignalR verifica se há um servidor de hub ligado. Se o serviço não conseguir encontrar um servidor de hub conectado, rejeita as ligações do cliente que chegam. Também pode utilizar a *API de Gestão* neste modo para gerir os clientes conectados diretamente através do Serviço Azure SignalR.
* `Serverless` o modo *não* permite qualquer ligação ao servidor. Ou seja, rejeitará todas as ligações do servidor. Todos os clientes devem estar em modo sem servidor. Os clientes conectam-se ao Serviço Azure SignalR, e os utilizadores geralmente usam tecnologias sem servidor, como *as Funções Azure* para lidar com lógicas de hub. [Veja um exemplo simples](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) que utiliza o modo sem servidor no Serviço Azure SignalR.
* `Classic` modo é um estado misto. Quando um hub tem uma ligação de servidor, o novo cliente será encaminhado para um servidor hub. Caso contrário, o cliente entrará no modo sem servidor. 

  Isto pode causar um problema. Por exemplo, se todas as ligações do servidor estiverem perdidas por um momento, alguns clientes entrarão em modo sem servidor em vez de encaminhar para um servidor de hub.

Aqui ficam algumas diretrizes para escolher um modo:
- Se não houver servidor de hub, escolha `Serverless` .
- Se todos os hubs tiverem servidores hub, escolha `Default` .
- Se alguns hubs têm servidores hub, mas outros não, pode escolher `Classic` , mas isso pode causar um problema. A melhor maneira é criar dois casos: um é `Serverless` - e o outro é `Default` .

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Existem diferenças de funcionalidades na utilização do Serviço Azure SignalR com ASP.NET SignalR?
Quando estiver a utilizar o Serviço Azure SignalR, algumas APIs e funcionalidades de ASP.NET SignalR não são suportadas:
- A capacidade de passar pelo estado arbitrário entre os clientes e o hub (muitas vezes `HubState` chamado) não é suportada.
- A `PersistentConnection` aula não é apoiada.
- *O transporte forever Frame* não é suportado.
- O Serviço Azure SignalR já não reproduz mensagens enviadas ao cliente quando o cliente está offline.
- Quando está a utilizar o Serviço Azure SignalR, o tráfego para uma ligação ao cliente é sempre encaminhado (também chamado *de pegajoso)* para uma instância do servidor de aplicações durante a duração da ligação.

O suporte para ASP.NET SignalR está focado na compatibilidade, pelo que nem todas as novas funcionalidades de ASP.NET o Core SignalR são suportados. Por exemplo, *o MessagePack* e *o Streaming* só estão disponíveis para aplicações ASP.NET Core SignalR.

Pode configurar o Serviço Azure SignalR para diferentes modos de serviço: `Classic` `Default` , e `Serverless` . O `Serverless` modo não é suportado para ASP.NET. A API do plano de dados também não é suportada.

## <a name="where-does-my-data-reside"></a>Onde residem os meus dados?

O Serviço Azure SignalR funciona como um serviço de processador de dados. Não armazena nenhum conteúdo do cliente, e a residência de dados é incluída pelo design. Se utilizar o Serviço Azure SignalR juntamente com outros serviços Azure, como o Azure Storage para diagnósticos, consulte [este livro branco](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) para obter orientações sobre como manter a residência de dados nas regiões de Azure.
