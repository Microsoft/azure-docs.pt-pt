---
title: Detalhes internos do Serviço SignalR do Azure
description: Conheça os internos do Serviço De Sinalização Azure, a arquitetura, as ligações e como os dados são transmitidos.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 8ba34edfc382f0f03abe080d78a6a47dcb65501b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105765"
---
# <a name="azure-signalr-service-internals"></a>Detalhes internos do Serviço SignalR do Azure

O Serviço De Sinalização Azure é construído em cima da estrutura de ASP.NET Core SignalR. Também suporta ASP.NET SignalR reimplementando ASP.NET protocolo de dados do SignalR para além do quadro central ASP.NET.

Pode migrar facilmente uma aplicação local ASP.NET Core SignalR ou ASP.NET aplicação SignalR para trabalhar com o SignalR Service, com algumas linhas de alteração de código.

O diagrama abaixo descreve a arquitetura típica quando utiliza o Serviço SignalR com o seu servidor de aplicações.

As diferenças entre a aplicação core signalr auto-hospedada ASP.NET são também discutidas.

![Arquitetura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Ligações ao servidor

O servidor de aplicação Core SignalR ASP.NET auto-hospedado ouve e liga os clientes diretamente.

Com o SignalR Service, o servidor de aplicações já não aceita ligações persistentes ao cliente, em vez disso:

1. Um `negotiate` ponto final é exposto pelo Serviço De Sinalização Azure SDK para cada hub.
1. Este ponto final responderá aos pedidos de negociação do cliente e redirecionará os clientes para o SignalR Service.
1. Eventualmente, os clientes estarão ligados ao Serviço SignalR.

Para mais informações, consulte [as ligações do Cliente.](#client-connections)

Uma vez iniciado o servidor de aplicações, 
- Para ASP.NET Core SignalR, o Serviço De Sinalização Azure SDK abre 5 ligações WebSocket por hub para o Serviço SignalR. 
- Para ASP.NET SignalR, o Serviço De Sinalização Azure SDK abre 5 ligações WebSocket por hub para o SignalR Service e uma por aplicação webSocket.

5 Ligações WebSocket é o valor predefinido que pode ser alterado na [configuração](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

As mensagens de e para os clientes serão multiplexadas nestas ligações.

Estas ligações permanecerão sempre ligadas ao Serviço SignalR. Se uma ligação do servidor for desligada para problemas de rede,
- todos os clientes que são servidos por esta desconexão de ligação do servidor (para mais informações sobre o mesmo, consulte [a transmissão de Dados entre o cliente e o servidor);](#data-transmit-between-client-and-server)
- a ligação do servidor começa a voltar a ligar-se automaticamente.

## <a name="client-connections"></a>Ligações de cliente

Quando utiliza o Serviço SignalR, os clientes ligam-se ao SignalR Service em vez do servidor de aplicações.
Existem duas etapas para estabelecer ligações persistentes entre o cliente e o Serviço SignalR.

1. O cliente envia um pedido de negociação para o servidor de aplicações. Com o Serviço De Sinalização Azure SDK, o servidor de aplicações devolve uma resposta de redirecionamento com URL do SignalR Service e ficha de acesso.

- Para ASP.NET Core SignalR, uma resposta de redirecionamento típica parece:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Para ASP.NET SignalR, uma resposta de redirecionamento típica parece:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Depois de receber a resposta de redirecionamento, o cliente utiliza o novo URL e acesso para iniciar o processo normal de ligação ao SignalR Service.

Saiba mais sobre ASP.NET [protocolos](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)de transporte do Core SignalR.

## <a name="data-transmit-between-client-and-server"></a>Transmissão de dados entre cliente e servidor

Quando um cliente está ligado ao Serviço SignalR, o tempo de execução do serviço encontrará uma ligação de servidor para servir este cliente
- Este passo acontece apenas uma vez, e é um mapeamento um-para-um entre as ligações do cliente e do servidor.
- O mapeamento é mantido no Serviço SignalR até que o cliente ou servidor se desconecte.

Neste momento, o servidor de aplicações recebe um evento com informações do novo cliente. Uma ligação lógica com o cliente é criada no servidor de aplicações. O canal de dados é estabelecido de cliente para servidor de aplicação, via SignalR Service.

O serviço SignalR transmite dados do cliente para o servidor de aplicações de emparelhamento. E os dados do servidor de aplicações serão enviados para os clientes mapeados.

Como pode ver, o Serviço De Sinalização Azure é essencialmente uma camada de transporte lógica entre servidor de aplicações e clientes. Todas as ligações persistentes são descarregadas para o Serviço SignalR.
O servidor de aplicações só precisa de lidar com a lógica do negócio na classe hub, sem se preocupar com as ligações do cliente.
