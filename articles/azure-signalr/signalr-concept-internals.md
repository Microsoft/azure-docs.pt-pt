---
title: Detalhes internos do Serviço SignalR do Azure
description: Saiba mais sobre os elementos internos do serviço Signalr do Azure, a arquitetura, as conexões e como os dados são transmitidos.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157606"
---
# <a name="azure-signalr-service-internals"></a>Detalhes internos do Serviço SignalR do Azure

O serviço de Signaler do Azure é criado com base na estrutura do Signalr ASP.NET Core. Ele também dá suporte ao Signalr ASP.NET como um recurso de visualização.

> Para dar suporte ao Signalr ASP.NET, o serviço de Signaler do Azure reimplementa o protocolo de dados do Signalr ASP.NET na parte superior da estrutura de ASP.NET Core

Você pode migrar facilmente um aplicativo de ASP.NET Core de sinalização local para trabalhar com o serviço de sinalização, com algumas linhas de alteração de código.

O diagrama a seguir descreve a arquitetura típica quando você usa o serviço Signalr com o servidor de aplicativos.

As diferenças do aplicativo Signalr de ASP.NET Core auto-hospedado também são discutidas.

![Arquitetura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Conexões do servidor

O servidor de aplicativo sinalizador de ASP.NET Core auto-hospedado escuta e conecta os clientes diretamente.

Com o serviço Signalr, o servidor de aplicativos não aceita mais conexões de cliente persistentes, em vez disso:

1. Um ponto de extremidade `negotiate` é exposto pelo SDK do serviço de Signaler do Azure para cada Hub.
1. Esse ponto de extremidade responderá às solicitações de negociação do cliente e redirecionará os clientes para o serviço Signalr.
1. Eventualmente, os clientes serão conectados ao serviço de Signalr.

Para obter mais informações, consulte [conexões de cliente](#client-connections).

Depois que o servidor de aplicativos for iniciado, 
- Para ASP.NET Core Signalr, o SDK do serviço de Signaler do Azure abre 5 conexões WebSocket por Hub para o serviço Signalr. 
- Para o Signalr ASP.NET, o SDK do serviço de Signaler do Azure abre 5 conexões WebSocket por Hub para o serviço Signalr e uma por conexão WebSocket por aplicativo.

5 conexões WebSocket é o valor padrão que pode ser alterado na [configuração](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Mensagens de e para clientes serão multiplexados nessas conexões.

Essas conexões permanecerão conectadas ao serviço Signalr o tempo todo. Se uma conexão de servidor estiver desconectada para o problema de rede,
- todos os clientes servidos por essa conexão de servidor desconectar (para obter mais informações sobre ele, consulte [transmissão de dados entre cliente e servidor](#data-transmit-between-client-and-server));
- a conexão do servidor inicia a reconexão automática.

## <a name="client-connections"></a>Ligações de cliente

Quando você usa o serviço de sinalização, os clientes se conectam ao serviço de sinalização em vez do servidor de aplicativos.
Há duas etapas para estabelecer conexões persistentes entre o cliente e o serviço Signalr.

1. O cliente envia uma solicitação de negociação para o servidor de aplicativos. Com o SDK do serviço de Signaler do Azure, o servidor de aplicativos retorna uma resposta de redirecionamento com a URL e o token de acesso do serviço de sinal

- Para ASP.NET Core Signalr, uma resposta de redirecionamento típica é semelhante a:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Para o Signalr ASP.NET, uma resposta de redirecionamento típica é semelhante a:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Depois de receber a resposta de redirecionamento, o cliente usa a nova URL e o token de acesso para iniciar o processo normal para se conectar ao serviço Signalr.

Saiba mais sobre os [protocolos de transporte](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)do ASP.NET Core signalr.

## <a name="data-transmit-between-client-and-server"></a>Transmissão de dados entre o cliente e o servidor

Quando um cliente está conectado ao serviço Signalr, o tempo de execução do serviço encontrará uma conexão com o servidor para atender a esse cliente
- Essa etapa ocorre apenas uma vez e é um mapeamento de um para um entre as conexões de cliente e servidor.
- O mapeamento é mantido no serviço de sinalização até que o cliente ou servidor se desconecte.

Neste ponto, o servidor de aplicativos recebe um evento com informações do novo cliente. Uma conexão lógica com o cliente é criada no servidor de aplicativos. O canal de dados é estabelecido do cliente para o servidor de aplicativos, por meio do serviço Signalr.

O serviço signalr transmite dados do cliente para o servidor de aplicativos de emparelhamento. E os dados do servidor de aplicativos serão enviados aos clientes mapeados.

Como você pode ver, o serviço do Azure Signalr é essencialmente uma camada de transporte lógica entre o servidor de aplicativos e os clientes. Todas as conexões persistentes são descarregadas para o serviço Signalr.
O servidor de aplicativos só precisa lidar com a lógica de negócios na classe de Hub, sem se preocupar com as conexões de cliente.