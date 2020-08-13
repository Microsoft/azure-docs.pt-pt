---
title: O Serviço Azure SignalR faz perguntas frequentemente
description: Tenha acesso rápido a perguntas frequentes no Serviço Azure SignalR, sobre resolução de problemas e cenários típicos de utilização.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c944ae3a5d647cc457edd20a5d3dd0489e19e286
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192281"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço Azure SignalR está pronto para ser utilizado pela produção?

Sim.
Para o nosso anúncio de disponibilidade geral, consulte [o Serviço Azure SignalR agora geralmente disponível](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET o Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) está totalmente suportado.

O suporte para ASP.NET SignalR ainda está na *pré-visualização pública*. Aqui está um [exemplo de código.](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A ligação Cliente fecha-se com a mensagem de erro "Não há servidor disponível". O que é que isso significa?

Este erro ocorre apenas quando os clientes estão a enviar mensagens para o Serviço SignalR.

Se não tiver qualquer servidor de aplicação e utilizar apenas a API do Serviço SignalR REST, este comportamento é **por design**.
Na arquitetura sem servidor, as ligações do cliente estão no modo **LISTEN** e não enviarão nenhuma mensagem para o Serviço SignalR.
Leia mais na [REST API.](./signalr-quickstart-rest-api.md)

Se tiver servidores de aplicações, esta mensagem de erro significa que nenhum servidor de aplicação está ligado à sua instância do Serviço SignalR.

As causas possíveis são:
- Nenhum servidor de aplicações está ligado ao Serviço SignalR. Verifique os registos do servidor de aplicações para obter possíveis erros de ligação. Este caso é raro em definições de alta disponibilidade com mais de um servidor de aplicação.
- Existem problemas de conectividade com as instâncias do Serviço SignalR. Esta questão é transitória e irá automaticamente recuperar.
Se persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou crie um pedido de apoio em [Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando existem vários servidores de aplicações, as mensagens do cliente são enviadas para todos os servidores ou apenas um deles?

É um-para-um mapeamento entre o cliente e o servidor de aplicações. As mensagens de um cliente são sempre enviadas para o mesmo servidor de aplicações.

O mapeamento entre o cliente e o servidor de aplicações será mantido até que o servidor de cliente ou aplicação se desligue.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicações está em baixo, como posso encontrá-lo e ser notificado?

O Serviço SignalR monitoriza os batimentos cardíacos dos servidores de aplicações.
Se os batimentos cardíacos não forem recebidos por um determinado período de tempo, o servidor de aplicação é considerado offline. Todas as ligações do cliente mapeadas a este servidor de aplicações serão desligadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Porque é que a minha `IUserIdProvider` exceção ao mudar de ASP.NET Core SignalR SDK para Azure SignalR Service SDK?

O parâmetro `HubConnectionContext context` é diferente entre ASP.NET Core SignalR SDK e Azure SignalR Service SDK quando é `IUserIdProvider` chamado.

Em ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da ligação física do cliente com valores válidos para todas as propriedades.

No Azure SignalR Service SDK, `HubConnectionContext context` é o contexto da ligação lógica do cliente. A ligação física do cliente está ligada à instância do Serviço SignalR, pelo que apenas um número limitado de propriedades são fornecidas.

Por enquanto, `HubConnectionContext.GetHttpContext()` `HubConnectionContext.User` apenas estão disponíveis para acesso.
Pode consultar o código fonte [aqui.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço SignalR como configurando-os no lado do servidor com ASP.NET Core SignalR? Por exemplo, desativar o transporte WebSocket?

Não.

O Serviço Azure SignalR fornece os três transportes que ASP.NET o Core SignalR suporta por padrão. Não é configurável. O Serviço SignalR tratará de ligações e transportes para todas as ligações do cliente.

Pode configurar transportes do lado do cliente, conforme documentado [aqui.](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado de métricas como a contagem de mensagens ou a contagem de ligação mostrada no portal Azure? Que tipo de agregação devo escolher?

Pode encontrar os detalhes sobre como calcular estas métricas [aqui.](signalr-concept-messages-and-connections.md)

Na folha geral dos recursos do Serviço Azure SignalR, já escolhemos o tipo de agregação apropriado para si. E se fores à lâmina das Métricas, podes tomar o tipo de agregação [aqui](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) como referência.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Qual é o significado do modo de `Default` / `Serverless` / `Classic` serviço? Como posso escolher?

Modos:
* `Default` o modo **requer** o servidor do hub. Quando não existe nenhuma ligação de servidor disponível para o hub, o cliente tenta ligar-se a este hub falha.
* `Serverless` modo **NÃO** permite qualquer ligação ao servidor, ou seja, rejeitará todas as ligações do servidor, todos os clientes devem em modo sem servidor.
* `Classic` modo é um estado misto. Quando um hub tiver ligação ao servidor, o novo cliente será encaminhado para o servidor hub, caso contrário, o cliente entrará no modo sem servidor.

  Isto pode causar algum problema, por exemplo, todas as ligações do servidor estão perdidas por um momento, alguns clientes entrarão em modo sem servidor, em vez de rota para o servidor hub.

Escolha:
1. Sem servidor de hub, escolha `Serverless` .
1. Todos os hubs têm servidores hub, `Default` escolha.
1. Alguns dos hubs têm servidores hub, outros não, `Classic` escolhem, mas isto pode causar algum problema, a melhor maneira é criar dois casos, um é `Serverless` , outro é `Default` .

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Alguma diferença de funcionalidade ao utilizar o Azure SignalR para ASP.NET SignalR?
Ao utilizar o Azure SignalR, algumas APIs e funcionalidades de ASP.NET SignalR já não são suportadas:
- A capacidade de passar pelo estado arbitrário entre os clientes e o hub (muitas vezes `HubState` chamado) não é suportada quando se utiliza o Azure SignalR
- `PersistentConnection` classe ainda não é suportada ao usar Azure SignalR
- **O transporte Forever Frame** não é suportado quando se utiliza O Sinal de Azure
- Azure SignalR já não reproduz mensagens enviadas ao cliente quando o cliente está offline
- Ao utilizar o Azure SignalR, o tráfego para uma ligação ao cliente é sempre encaminhado (aka. **pegajoso)** a uma instância do servidor de aplicações para a duração da ligação

O suporte para ASP.NET SignalR está focado na compatibilidade, pelo que nem todas as novas funcionalidades de ASP.NET o Core SignalR são suportados. Por exemplo, **o MessagePack**, **Streaming**, etc., só está disponível para aplicações ASP.NET Core SignalR.

O Serviço SignalR pode ser configurado para diferentes modos de serviço: `Classic` / `Default` / `Serverles` s. Neste suporte ASP.NET, o `Serverless` modo não é suportado. A API do plano de dados também não é suportada.

## <a name="where-do-my-data-reside"></a>Onde residem os meus dados?

O Serviço Azure SignalR está a funcionar como um serviço de processador de dados. Não armazenará nenhum conteúdo do cliente e a residência de dados é prometida por design. Se utilizar o Serviço Azure SignalR juntamente com outros serviços Azure, como o Azure Storage para diagnósticos, consulte [aqui](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) a informação sobre como manter a residência de dados nas regiões de Azure.
