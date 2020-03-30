---
title: Serviço de Sinalização Azure frequentemente faz perguntas
description: Tenha acesso rápido a perguntas frequentes sobre o Serviço De Sinalização Azure, sobre resolução de problemas e cenários de utilização típicos.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75891256"
---
# <a name="azure-signalr-service-faq"></a>FaQ do serviço de sinalização Azure

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço de Sinalização Azure está pronto para utilização da produção?

Sim.
Para o nosso anúncio de disponibilidade geral, consulte [o Serviço De Sinalização Azure agora disponível em geral.](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente suportado.

O apoio ao ASP.NET SignalR ainda está na *pré-visualização pública.* Aqui está um exemplo de [código.](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A ligação cliente fecha com a mensagem de erro "Nenhum servidor disponível". O que é que isso significa?

Este erro ocorre apenas quando os clientes estão a enviar mensagens para o Serviço SignalR.

Se não tiver nenhum servidor de aplicação e utilizar apenas a API do Serviço SignalR, este comportamento é **por design**.
Na arquitetura sem servidor, as ligações do cliente estão no modo **LISTEN** e não enviarão nenhuma mensagem para o SignalR Service.
Leia mais na [Rest API](./signalr-quickstart-rest-api.md).

Se tiver servidores de aplicação, esta mensagem de erro significa que nenhum servidor de aplicação está ligado à sua instância de Serviço SignalR.

As possíveis causas são:
- Nenhum servidor de aplicação está ligado ao Serviço SignalR. Verifique os registos do servidor da aplicação para eventuais erros de ligação. Este caso é raro em configuração de alta disponibilidade com mais de um servidor de aplicação.
- Existem problemas de conectividade com instâncias do Serviço SignalR. Esta questão é transitória e vai recuperar automaticamente.
Se persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou crie um pedido de apoio no [Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando existem vários servidores de aplicações, as mensagens do cliente são enviadas para todos os servidores ou apenas para um deles?

É um a um mapeamento entre cliente e servidor de aplicações. As mensagens de um cliente são sempre enviadas para o mesmo servidor de aplicações.

O mapeamento entre o cliente e o servidor de aplicações será mantido até que o cliente ou o servidor de aplicação se desconectem.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicações está avariado, como posso encontrá-lo e ser notificado?

O Serviço SignalR monitoriza os batimentos cardíacos dos servidores de aplicações.
Se os batimentos cardíacos não forem recebidos durante um determinado período de tempo, o servidor de aplicação é considerado offline. Todas as ligações do cliente mapeadas para este servidor de aplicações serão desligadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Porque é `IUserIdProvider` que o meu costume abre exceções ao mudar de ASP.NET Core SignalR SDK para O Serviço de Sinalização Azure SDK?

O parâmetro `HubConnectionContext context` é diferente entre ASP.NET Serviço De Sinalizador Central SDK e Serviço de Sinalização Azure SDK quando `IUserIdProvider` é chamado.

Em ASP.NET Core `HubConnectionContext context` SignalR, é o contexto da ligação física do cliente com valores válidos para todas as propriedades.

No Serviço De Sinalização Azure SDK, `HubConnectionContext context` é o contexto da conexão lógica do cliente. A ligação física do cliente está ligada à instância do Serviço SignalR, pelo que apenas um número limitado de propriedades são fornecidas.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Pode verificar o código fonte [aqui.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no SignalR Service como configurar no lado do servidor com ASP.NET Core SignalR? Por exemplo, desativar o transporte WebSocket?

Não.

O Serviço De Sinalização Azure fornece os três transportes que ASP.NET suportes Core SignalR por defeito. Não é configurável. O SignalR Service tratará de ligações e transportes para todas as ligações do cliente.

Pode configurar os transportes do lado do cliente conforme documentado [aqui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).
