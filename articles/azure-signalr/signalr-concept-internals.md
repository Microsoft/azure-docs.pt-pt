---
title: Detalhes internos do Serviço SignalR do Azure
description: Uma visão geral dos aspectos internos do serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cbcdfccfdca1dbed3b766b3f50295b1d355b3478
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555082"
---
# <a name="azure-signalr-service-internals"></a>Detalhes internos do Serviço SignalR do Azure

Serviço Azure SignalR é criado sobre SignalR do ASP.NET Core framework. Também suporta o SignalR de ASP.NET como uma funcionalidade de pré-visualização.

> Para suportar o SignalR de ASP.NET, o serviço Azure SignalR reimplements protocolo de dados do ASP.NET SignalR sobre a estrutura do ASP.NET Core

Pode migrar facilmente uma aplicação local do SignalR do ASP.NET Core para trabalhar com o serviço SignalR, com algumas linhas de alteração de código.

O diagrama abaixo descreve a arquitetura típica ao utilizar o serviço SignalR com o seu servidor de aplicativos.

As diferenças de aplicativo do ASP.NET Core SignalR autoalojado são discutidas também.

![Arquitetura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Ligações de servidor

Servidor de aplicativos do ASP.NET Core SignalR autoalojado fica à escuta e liga-se os clientes diretamente.

Com o serviço SignalR, o servidor de aplicações não está mais aceitando conexões de cliente persistentes, em vez disso:

1. A `negotiate` ponto final é exposto pelo SDK do serviço Azure SignalR para cada hub.
1. Este ponto final responderá aos pedidos de negociação do cliente e redirecionar os clientes para o serviço SignalR.
1. Eventualmente, os clientes serão ligados ao serviço SignalR.

Para obter mais informações, consulte [ligações de cliente](#client-connections).

Assim que o servidor de aplicativo é iniciado, 
- Para SignalR do ASP.NET Core, o SDK do serviço Azure SignalR abre 5 ligações de WebSocket por hub para o serviço SignalR. 
- Para o SignalR de ASP.NET, o SDK do serviço Azure SignalR abre 5 ligações de WebSocket por hub para o serviço SignalR e um por conexão WebSocket de aplicação.

5 de WebSocket de ligações é o valor predefinido, que pode ser alterado no [configuração](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Mensagens de e para os clientes irão ser multiplexadas para estas ligações.

Estas ligações irão permanecer ligadas para o serviço SignalR o tempo todo. Se uma ligação de servidor é desconectada para o problema de rede,
- todos os clientes que são servidos por este desconexão de ligação do servidor (para obter mais informações sobre isso, consulte [dados transmitem entre cliente e servidor](#data-transmit-between-client-and-server));
- a ligação ao servidor inicia automaticamente a restabelecer ligação.

## <a name="client-connections"></a>Ligações de cliente

Quando utiliza o serviço SignalR, os clientes ligam ao serviço SignalR, em vez de servidor de aplicações.
Existem dois passos para estabelecer ligações persistentes entre o cliente e o serviço de SignalR.

1. Cliente envia um pedido de negociação para o servidor de aplicações. Com o SDK do serviço Azure SignalR, o servidor de aplicativos devolve uma resposta de redirecionamento com o token de acesso e o URL do serviço de SignalR.

- Para o SignalR do ASP.NET Core, uma resposta de redirecionamento típico é semelhante a:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Para o SignalR de ASP.NET, uma resposta de redirecionamento típico é semelhante a:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Depois de receber a resposta de redirecionamento, o cliente utiliza o novo URL e o token de acesso para iniciar o processo normal para ligar ao serviço de SignalR.

Saiba mais sobre o ASP.NET Core SignalR [protocolos de transporte](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md).

## <a name="data-transmit-between-client-and-server"></a>Transmissão de dados entre cliente e servidor

Quando um cliente está ligado ao serviço do SignalR, o tempo de execução do serviço irá encontrar uma ligação ao servidor para atender a esse cliente
- Este passo ocorre apenas uma vez e é um mapeamento entre as ligações de cliente e servidor.
- O mapeamento é mantido no serviço SignalR até que o cliente ou servidor desliga.

Neste momento, o servidor de aplicações recebe um evento com as informações do cliente novo. Uma ligação lógica para o cliente é criada no servidor de aplicativos. O canal de dados é estabelecido de cliente para servidor de aplicações, através do serviço SignalR.

Serviço SignalR transmite dados do cliente para o servidor emparelhamento de aplicações. E dados a partir do servidor de aplicações serão enviados para os clientes mapeados.

Como pode ver, o serviço Azure SignalR é essencialmente uma camada de transporte lógico entre o servidor de aplicações e clientes. Todas as ligações persistentes são descarregadas para serviço SignalR.
Servidor de aplicações só precisa lidar com a lógica de negócios na classe de hub, sem se preocupar sobre ligações de cliente.