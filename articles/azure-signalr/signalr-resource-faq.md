---
title: O Serviço Azure SignalR faz perguntas frequentemente
description: Obtenha respostas para perguntas frequentes sobre o Serviço Azure SignalR, incluindo resolução de problemas e cenários típicos de utilização.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579312"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço Azure SignalR está pronto para ser utilizado pela produção?

Sim, tanto o suporte para [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) como [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) estão geralmente disponíveis.

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

N.º

O Serviço Azure SignalR fornece os três transportes que ASP.NET o Core SignalR suporta por padrão. Não é configurável. O Serviço Azure SignalR tratará de ligações e transportes para todas as ligações do cliente.

Pode configurar os transportes do lado do cliente, conforme documentado na [configuração do Core SignalR ASP.NET](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado de métricas como a contagem de mensagens ou a contagem de ligação mostrada no portal Azure? Que tipo de agregação devo escolher?

Pode encontrar detalhes sobre a cálculo destas métricas em [Mensagens e ligações no Serviço Azure SignalR](signalr-concept-messages-and-connections.md).

No painel geral dos recursos do Serviço Azure SignalR, já escolhemos o tipo de agregação apropriado para si. Se for ao painel de métricas, pode levar o tipo de agregação a [Mensagens e ligações no Serviço Azure SignalR](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) como referência.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual é o significado dos `Default` `Serverless` modos de `Classic` serviço e dos modos de serviço? Como posso escolher?

Para novas aplicações, apenas deve ser utilizado o modo predefinido e sem servidor. A principal diferença é se tem servidores de aplicações que estabelecem ligações de servidor ao serviço (isto é, usar `AddAzureSignalR()` para ligar ao serviço). Se sim utilizar o modo predefinido, de outra forma use o modo sem servidor.

O modo clássico é projetado para retrocompatibilidade para aplicações existentes, pelo que não deve ser utilizado para novas aplicações.

Para mais informações sobre o modo de serviço [neste doc](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Posso enviar mensagem do cliente em modo sem servidor?

Pode enviar mensagem do cliente se configurar a montante no seu caso SignalR. A montante é um conjunto de pontos finais que podem receber mensagens e eventos de ligação do serviço SignalR. Se não houver configuração a montante, as mensagens do cliente serão ignoradas.

Para mais informações sobre a montante consulte [este doc](concept-upstream.md).

A montante está atualmente em pré-visualização pública.

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