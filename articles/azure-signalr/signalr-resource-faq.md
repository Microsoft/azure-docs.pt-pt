---
title: Serviço Azure SignalR perguntas mais frequentes
description: Perguntas frequentes para o serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 09788f4ded66b43fd5ecae20301a28cd01d77320
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554534"
---
# <a name="azure-signalr-service-faq"></a>FAQ do serviço Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>É o serviço Azure SignalR pronto para uso em produção?

Sim.
Para o nosso anúncio da disponibilidade geral, consulte [serviço Azure SignalR agora em disponibilidade geral](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente suportado.

Suporte para o ASP.NET SignalR é ainda na *pré-visualização pública*. Aqui está uma [exemplo de código](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Não fecha a ligação de cliente com a mensagem de erro "Nenhum servidor disponível". O que significa?

Este erro ocorre apenas quando os clientes estão a enviar mensagens para o serviço de SignalR.

Se não tiver qualquer servidor de aplicação e utilizar apenas a API de REST do SignalR serviço, esse comportamento é **propositado**.
Arquitetura sem servidor, as ligações de cliente estão na **ESCUTAR** modo e irá não enviar as mensagens para o serviço SignalR.
Leia mais em [REST API](./signalr-quickstart-rest-api.md).

Se tiver servidores de aplicações, esta mensagem de erro significa que nenhum servidor de aplicativo está ligado à sua instância de serviço SignalR.

As causas possíveis são:
- Nenhum servidor de aplicativo está ligado com o serviço SignalR. Verifique os registos do servidor de aplicação para erros de ligação possíveis. Neste caso, é raro na definição de elevada disponibilidade com mais do que um servidores de aplicativos.
- Existem problemas de conectividade com instâncias de serviço SignalR. Este problema é transitório e vai recuperar automaticamente.
Se isto persistir durante mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [criar um pedido de suporte no Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicações, são o cliente mensagens enviadas para todos os servidores ou apenas um deles?

Ele é o mapeamento entre o servidor de cliente e a aplicação. As mensagens de um cliente sempre são enviadas para o mesmo servidor de aplicação.

O mapeamento entre o servidor de cliente e a aplicação será mantido até que o aplicativo cliente ou servidor desliga.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicação estiver em baixo, como posso encontrá-lo e obter notificado?

Serviço SignalR monitoriza os heartbeats do servidores de aplicações.
Se heartbeats não são recebidos para um período de tempo especificado, o servidor de aplicações é considerado offline. Todas as ligações de cliente mapeadas para este servidor de aplicações serão desligadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Por que motivo faz meu personalizado `IUserIdProvider` lançar a exceção, quando a mudança do SDK do SignalR do ASP.NET Core para o SDK do serviço Azure SignalR?

O parâmetro `HubConnectionContext context` é diferente entre o SDK de SignalR do ASP.NET Core e o SDK do serviço Azure SignalR quando `IUserIdProvider` é chamado.

No ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da ligação de cliente físicos com valores válidos para todas as propriedades.

No SDK do serviço Azure SignalR, `HubConnectionContext context` é o contexto de conexão com a lógica de cliente. A ligação de cliente físico está ligada à instância do serviço SignalR, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Pode verificar o código-fonte [aqui](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Pode configurar os transportes disponíveis no serviço SignalR como configurá-lo no lado do servidor com o SignalR do ASP.NET Core? Por exemplo, desativar o transporte de WebSocket?

Não.

Serviço Azure SignalR fornece todos os três transportes que suporta o SignalR do ASP.NET Core por predefinição. Não é configurável. Serviço SignalR processará as ligações e transportes para todas as ligações de cliente.

Pode configurar transportes de lado do cliente, conforme documentado [aqui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
