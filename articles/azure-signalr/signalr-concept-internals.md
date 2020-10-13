---
title: Detalhes internos do Serviço SignalR do Azure
description: Conheça os interiores do Serviço Azure SignalR, a arquitetura, as ligações e a forma como os dados são transmitidos.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3fc6971c66d06ae9f25584f5be28b051075bfa49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88921975"
---
# <a name="azure-signalr-service-internals"></a>Detalhes internos do Serviço SignalR do Azure

O Serviço Azure SignalR é construído em cima de ASP.NET estrutura do Core SignalR. Também apoia ASP.NET SignalR, reimplementando ASP.NET protocolo de dados do SignalR no topo do quadro ASP.NET Core.

Pode migrar facilmente uma aplicação de ASP.NET Core SignalR local ou ASP.NET aplicação SignalR para trabalhar com o Serviço SignalR, com algumas linhas de mudança de código.

O diagrama abaixo descreve a arquitetura típica quando utiliza o Serviço SignalR com o seu servidor de aplicações.

As diferenças entre a aplicação core signalr ASP.NET auto-acolhido também são discutidas.

![Arquitetura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Conexões de servidor

O servidor de aplicação Core SignalR ASP.NET auto-alojado ouve e liga diretamente os clientes.

Com o Serviço SignalR, o servidor de aplicações já não aceita ligações persistentes do cliente:

1. Um `negotiate` ponto final é exposto pelo Azure SignalR Service SDK para cada hub.
1. Este ponto final irá responder aos pedidos de negociação do cliente e redirecionar os clientes para o Serviço SignalR.
1. Eventualmente, os clientes serão ligados ao Serviço SignalR.

Para mais informações, consulte [as ligações do Cliente.](#client-connections)

Uma vez iniciado o servidor de aplicações, 
- Para ASP.NET Core SignalR, o Serviço Azure SignalR SDK abre 5 ligações WebSocket por hub ao Serviço SignalR. 
- Para ASP.NET SignalR, o Serviço Azure SignalR SDK abre 5 ligações WebSocket por hub ao SignalR Service e uma por aplicação Ligação WebSocket.

5 As ligações WebSocket são o valor predefinido que pode ser alterado na [configuração](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

As mensagens de e para os clientes serão multiplexed nestas ligações.

Estas ligações permanecerão sempre ligadas ao Serviço SignalR. Se uma ligação do servidor for desligada para problemas de rede,
- todos os clientes que são servidos por esta ligação de servidor desligam (para mais informações sobre o mesmo, consulte [dados transmitidos entre cliente e servidor](#data-transmit-between-client-and-server));
- a ligação do servidor começa a reconectar-se automaticamente.

## <a name="client-connections"></a>Ligações de cliente

Quando utiliza o Serviço SignalR, os clientes ligam-se ao Serviço SignalR em vez do servidor de aplicações.
Existem duas etapas para estabelecer ligações persistentes entre o cliente e o Serviço SignalR.

1. O cliente envia um pedido de negociação para o servidor de aplicações. Com o Serviço Azure SignalR SDK, o servidor de aplicação devolve uma resposta de redirecionamento com o URL do Serviço SignalR e o token de acesso.

- Para ASP.NET Core SignalR, uma resposta típica de redirecionamento parece:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Para ASP.NET SignalR, uma resposta típica de redirecionamento parece:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Depois de receber a resposta de redirecionamento, o cliente utiliza o novo URL e o token de acesso para iniciar o processo normal para ligar ao Serviço SignalR.

Saiba mais sobre ASP.NET [protocolos](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)de transporte do Core SignalR.

## <a name="data-transmit-between-client-and-server"></a>Os dados transmitem entre cliente e servidor

Quando um cliente está ligado ao Serviço SignalR, o tempo de funcionação do serviço encontrará uma ligação de servidor para servir este cliente
- Este passo só acontece uma vez, e é um mapeamento de um para um entre as ligações do cliente e do servidor.
- O mapeamento é mantido no Serviço SignalR até que o cliente ou servidor se desligue.

Neste momento, o servidor de aplicações recebe um evento com informações do novo cliente. Uma ligação lógica ao cliente é criada no servidor de aplicações. O canal de dados é estabelecido de cliente para servidor de aplicações, via Serviço SignalR.

O Serviço SignalR transmite dados do cliente para o servidor de aplicações de emparelhamento. E os dados do servidor de aplicações serão enviados para os clientes mapeados.

O Serviço SignalR não guarda nem armazena os dados do cliente, todos os dados do cliente recebidos são transmitidos para servidor ou clientes alvo em tempo real.

Como pode ver, o Serviço Azure SignalR é essencialmente uma camada lógica de transporte entre servidor de aplicações e clientes. Todas as ligações persistentes são descarregadas para o Serviço SignalR.
O servidor de aplicações apenas precisa de lidar com a lógica de negócio na classe hub, sem se preocupar com as ligações com o cliente.