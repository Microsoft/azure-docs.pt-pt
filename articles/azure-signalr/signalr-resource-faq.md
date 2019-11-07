---
title: Perguntas frequentes sobre o serviço Signaler do Azure
description: Perguntas frequentes sobre o serviço de Signaler do Azure.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 771124d0b8ca15bf72501fdeff8c31d0a43050b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578674"
---
# <a name="azure-signalr-service-faq"></a>Perguntas frequentes sobre o serviço do Azure Signalr

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O serviço Signaler do Azure está pronto para uso em produção?

Sim.
Para nosso anúncio de disponibilidade geral, consulte o [serviço Azure signalr agora está disponível para o público geral](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

O [sinal de ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente suportado.

O suporte para o Signalr ASP.NET ainda está na *Visualização pública*. Aqui está um [exemplo de código](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A conexão de cliente fecha com a mensagem de erro "nenhum servidor disponível". O que isso significa?

Esse erro ocorre somente quando os clientes estão enviando mensagens para o serviço Signalr.

Se você não tiver nenhum servidor de aplicativos e usar apenas a API REST do serviço Signalr, esse comportamento será **por design**.
Na arquitetura sem servidor, as conexões de cliente estão no modo de **escuta** e não enviarão nenhuma mensagem para o serviço signalr.
Leia mais sobre a [API REST](./signalr-quickstart-rest-api.md).

Se você tiver servidores de aplicativos, essa mensagem de erro significa que nenhum servidor de aplicativos está conectado à sua instância de serviço do Signalr.

As possíveis causas são:
- Nenhum servidor de aplicativos está conectado com o serviço de sinalização. Verifique os logs do servidor de aplicativos para obter possíveis erros de conexão. Esse caso é raro na configuração de alta disponibilidade com mais de um servidor de aplicativos.
- Há problemas de conectividade com instâncias de serviço do Signalr. Esse problema é transitório e será recuperado automaticamente.
Se persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [crie uma solicitação de suporte no Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicativos, as mensagens do cliente são enviadas a todos os servidores ou apenas um deles?

É um mapeamento de um-para-um entre o cliente e o servidor de aplicativos. As mensagens de um cliente são sempre enviadas para o mesmo servidor de aplicativos.

O mapeamento entre cliente e servidor de aplicativos será mantido até que o cliente ou servidor de aplicativos se desconecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicativos estiver inativo, como posso encontrá-lo e ser notificado?

O serviço signalr monitora as pulsações dos servidores de aplicativos.
Se as pulsações não forem recebidas por um período de tempo especificado, o servidor de aplicativos será considerado offline. Todas as conexões de cliente mapeadas para este servidor de aplicativos serão desconectadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Por que o meu `IUserIdProvider` personalizado gera exceções ao alternar do SDK do Signalr ASP.NET Core para o SDK do serviço de Signaler do Azure?

O parâmetro `HubConnectionContext context` é diferente entre o SDK do Signalr ASP.NET Core e o SDK do serviço de Signaler do Azure quando `IUserIdProvider` é chamado.

No ASP.NET Core Signalr, `HubConnectionContext context` é o contexto da conexão de cliente físico com valores válidos para todas as propriedades.

No SDK do serviço de Signaler do Azure, `HubConnectionContext context` é o contexto da conexão de cliente lógica. A conexão do cliente físico é conectada à instância do serviço Signalr, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, somente `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Você pode verificar o código-fonte [aqui](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no serviço Signalr como configurá-los no lado do servidor com ASP.NET Core Signalr? Por exemplo, desabilitar o transporte WebSocket?

Não.

O serviço de Signaler do Azure fornece todos os três transportes que ASP.NET Core Signalr dá suporte por padrão. Ele não é configurável. O serviço signalr tratará conexões e transportes para todas as conexões de cliente.

Você pode configurar transportes do lado do cliente conforme documentado [aqui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
