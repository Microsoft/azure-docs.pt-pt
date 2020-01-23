---
title: Visão geral das APIs de nó de retransmissão do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da API do node. js para o serviço de retransmissão do Azure. Ele também mostra como usar o pacote de nó hyco-WS.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514515"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Visão geral da API do nó de retransmissão Conexões Híbridas

## <a name="overview"></a>Visão geral

O pacote de nó [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) para retransmissão do Azure conexões híbridas é criado e estende o pacote de NPM [' WS '](https://www.npmjs.com/package/ws) . Esse pacote reexporta todas as exportações desse pacote base e adiciona novas exportações que habilitam a integração com o recurso Conexões Híbridas do serviço de retransmissão do Azure. 

Os aplicativos existentes que `require('ws')` podem usar esse pacote com `require('hyco-ws')` em vez disso, o que também permite cenários híbridos nos quais um aplicativo pode escutar conexões WebSocket localmente de "dentro do firewall" e por meio de Conexões Híbridas, tudo ao mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs estão [documentadas no pacote principal ' WS '](https://github.com/websockets/ws/blob/master/doc/ws.md). Este artigo descreve como esse pacote é diferente da linha de base. 

As principais diferenças entre o pacote base e este ' hyco-WS ' é que ele adiciona uma nova classe de servidor, exportada via `require('hyco-ws').RelayedServer`e alguns métodos auxiliares.

### <a name="package-helper-methods"></a>Métodos auxiliares de pacote

Há vários métodos de utilitário disponíveis na exportação de pacote que você pode referenciar da seguinte maneira:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos auxiliares são para uso com este pacote, mas também podem ser usados por um servidor de nó para permitir que clientes Web ou de dispositivo criem ouvintes ou remetentes. O servidor usa esses métodos passando-os URIs que inserem tokens de curta duração. Esses URIs também podem ser usados com pilhas WebSocket comuns que não dão suporte à definição de cabeçalhos HTTP para o handshake WebSocket. A inserção de tokens de autorização no URI é suportada principalmente para os cenários de uso externo da biblioteca. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria um URI de ouvinte de conexão híbrida de retransmissão do Azure válido para o namespace e o caminho fornecidos. Esse URI pode ser usado com a versão de retransmissão da classe WebSocketServer.

- `namespaceName` (obrigatório)-o nome qualificado pelo domínio do namespace de retransmissão do Azure a ser usado.
- `path` (obrigatório)-o nome de uma conexão híbrida de retransmissão do Azure existente nesse namespace.
- `token` (opcional)-um token de acesso de retransmissão emitido anteriormente que é inserido no URI do ouvinte (consulte o exemplo a seguir).
- `id` (opcional)-um identificador de rastreamento que permite o acompanhamento de diagnóstico de ponta a ponta de solicitações.

O valor `token` é opcional e só deve ser usado quando não for possível enviar cabeçalhos HTTP junto com o handshake WebSocket, como é o caso com a pilha do WebSocket do W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria um URI de envio de conexão híbrida de retransmissão do Azure válido para o namespace e o caminho fornecidos. Esse URI pode ser usado com qualquer cliente WebSocket.

- `namespaceName` (obrigatório)-o nome qualificado pelo domínio do namespace de retransmissão do Azure a ser usado.
- `path` (obrigatório)-o nome de uma conexão híbrida de retransmissão do Azure existente nesse namespace.
- `token` (opcional)-um token de acesso de retransmissão emitido anteriormente que é inserido no URI de envio (consulte o exemplo a seguir).
- `id` (opcional)-um identificador de rastreamento que permite o acompanhamento de diagnóstico de ponta a ponta de solicitações.

O valor `token` é opcional e só deve ser usado quando não for possível enviar cabeçalhos HTTP junto com o handshake WebSocket, como é o caso com a pilha do WebSocket do W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um token de SAS (assinatura de acesso compartilhado) de retransmissão do Azure para o URI de destino fornecido, a regra SAS e a chave de regra SAS que é válida para o número determinado de segundos ou para uma hora do instante atual se o argumento de expiração for omitido.

- `uri` (obrigatório)-o URI para o qual o token deve ser emitido. O URI é normalizado para usar o esquema HTTP e as informações de cadeia de caracteres de consulta são removidas.
- `ruleName` (obrigatório)-nome da regra SAS para a entidade representada pelo URI fornecido ou para o namespace representado pela parte do host URI.
- `key` (obrigatório)-chave válida para a regra de SAS. 
- `expirationSeconds` (opcional)-o número de segundos até que o token gerado expire. Se não for especificado, o padrão será 1 hora (3600).

O token emitido confere os direitos associados à regra SAS especificada para a duração determinada.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Esse método é funcionalmente equivalente ao método `createRelayToken` documentado anteriormente, mas retorna o token anexado corretamente ao URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe WS. RelayedServer

A classe `hycows.RelayedServer` é uma alternativa à classe `ws.Server` que não escuta na rede local, mas os delegados que escutam o serviço de retransmissão do Azure.

As duas classes são basicamente compatíveis com o contrato, o que significa que um aplicativo existente usando a classe `ws.Server` pode ser facilmente alterado para usar a versão retransmitida. As principais diferenças estão no construtor e nas opções disponíveis.

#### <a name="constructor"></a>Construtor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

O Construtor `RelayedServer` dá suporte a um conjunto diferente de argumentos que o `Server`, pois ele não é um ouvinte autônomo ou pode ser inserido em uma estrutura de ouvinte HTTP existente. Há também menos opções disponíveis, pois o gerenciamento de WebSocket é basicamente delegado para o serviço de retransmissão.

Argumentos do Construtor:

- `server` (obrigatório)-o URI totalmente qualificado para um nome de conexão híbrida no qual escutar, normalmente construído com o método auxiliar WebSocket. createRelayListenUri ().
- `token` (obrigatório)-esse argumento contém uma cadeia de caracteres de token emitida anteriormente ou uma função de retorno de chamada que pode ser chamado para obter essa cadeia de caracteres de token. A opção de retorno de chamada é preferida, pois habilita a renovação de token.

#### <a name="events"></a>Eventos

`RelayedServer` instâncias emitem três eventos que permitem que você manipule solicitações de entrada, estabeleça conexões e detecte condições de erro. Você deve assinar o evento `connect` para manipular mensagens. 

##### <a name="headers"></a>conector

```JavaScript 
function(headers)
```

O evento `headers` é gerado logo antes de uma conexão de entrada ser aceita, permitindo a modificação dos cabeçalhos para enviar ao cliente. 

##### <a name="connection"></a>ligação

```JavaScript
function(socket)
```

Emitido quando uma nova conexão WebSocket é aceita. O objeto é do tipo `ws.WebSocket`, assim como no pacote base.


##### <a name="error"></a>erro

```JavaScript
function(error)
```

Se o servidor subjacente emitir um erro, ele será encaminhado aqui.  

#### <a name="helpers"></a>Auxiliares

Para simplificar a inicialização de um servidor retransmitido e a assinatura imediata de conexões de entrada, o pacote expõe uma função auxiliar simples, que também é usada nos exemplos, da seguinte maneira:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Esse método chama o construtor para criar uma nova instância do RelayedServer e, em seguida, assina o retorno de chamada fornecido para o evento ' Connection '.
 
##### <a name="relayedconnect"></a>relayedConnect

Simplesmente espelhando o auxiliar de `createRelayedServer` em Function, `relayedConnect` cria uma conexão de cliente e assina o evento ' Open ' no soquete resultante.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a retransmissão do Azure, visite estes links:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de retransmissão disponíveis](relay-api-overview.md)
