---
title: Descrição geral das APIs de nó do reencaminhamento do Azure | Documentos da Microsoft
description: Descrição geral da API de nó de reencaminhamento
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
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 794e797e504d6064c13ffe0a4ed131e668d86e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421612"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Descrição geral da API de nó de ligações híbridas de reencaminhamento

## <a name="overview"></a>Descrição geral

O [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) pacote de nó para ligações híbridas do reencaminhamento do Azure baseia-se e expande o ["ws"](https://www.npmjs.com/package/ws) pacote NPM. Este pacote novamente Exporta todas as exportações do pacote base e adiciona novas exportações que ativar a integração com a funcionalidade de ligações híbridas do serviço de reencaminhamento do Azure. 

Os aplicativos existentes que `require('ws')` pode utilizar este pacote com `require('hyco-ws')` em vez disso, o que permite também cenários híbridos, em que um aplicativo pode escutar para ligações de WebSocket localmente a partir de "no interior da firewall" e através de ligações híbridas, tudo no ao mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs são [documentados no pacote principal "ws"](https://github.com/websockets/ws/blob/master/doc/ws.md). Este artigo descreve como este pacote difere essa linha de base. 

As principais diferenças entre o pacote básico e este 'hyco-ws"é que ele adiciona uma nova classe de servidor, exportada por meio de `require('hyco-ws').RelayedServer`e alguns métodos auxiliares.

### <a name="package-helper-methods"></a>Métodos de programa auxiliar do pacote

Existem vários métodos de utilitário disponíveis na exportação de pacote que pode fazer referência a da seguinte forma:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos auxiliares são para utilização com este pacote, mas também podem ser utilizados por um servidor de nó para habilitar clientes web ou de dispositivo criar serviços de escuta ou remetentes. O servidor utiliza esses métodos, passando-os URIs que incorpore tokens de curta duração. Estes URIs também pode ser utilizado com as pilhas de WebSocket comuns que não suportam a cabeçalhos HTTP de definição para o handshake do WebSocket. Incorporação de tokens de autorização para o URI é suportado principalmente para esses cenários de utilização da biblioteca externa. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria uma escuta de ligação de híbridas de reencaminhamento do Azure válida URI para o espaço de nomes especificado e o caminho. Este URI, em seguida, pode ser utilizado com a versão de reencaminhamento da classe WebSocketServer.

- `namespaceName` (obrigatório) - o nome de domínio qualificado do espaço de nomes de reencaminhamento do Azure para utilizar.
- `path` (obrigatório) - o nome de uma ligação híbrida de reencaminhamento do Azure existente nesse espaço de nomes.
- `token` (opcional) – um emitidos anteriormente reencaminhamento token de acesso que está incorporada no serviço de escuta URI (veja o exemplo a seguir).
- `id` (opcional) – um identificador de controlo que permite o controlo de diagnóstico de ponto-a-ponto de pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP, juntamente com o handshake do WebSocket, tal como acontece com a pilha de WebSocket de W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria um envio de ligação de híbridas de reencaminhamento do Azure URI válido para o espaço de nomes especificado e o caminho. Este URI pode ser utilizado com qualquer cliente WebSocket.

- `namespaceName` (obrigatório) - o nome de domínio qualificado do espaço de nomes de reencaminhamento do Azure para utilizar.
- `path` (obrigatório) - o nome de uma ligação híbrida de reencaminhamento do Azure existente nesse espaço de nomes.
- `token` (opcional) – um emitidos anteriormente reencaminhamento token de acesso que está incorporada no envio URI (veja o exemplo a seguir).
- `id` (opcional) – um identificador de controlo que permite o controlo de diagnóstico de ponto-a-ponto de pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP, juntamente com o handshake do WebSocket, tal como acontece com a pilha de WebSocket de W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um token de assinatura de acesso partilhado do Azure Relay (SAS) para o determinado URI de destino, a regra SAS e a regra da chave SAS que é válida para o determinado número de segundos ou para uma hora de instante atual se o argumento de expiração for omitido.

- `uri` (obrigatório) - o URI para o qual o token é emitido. O URI é normalizado para utilizar o esquema HTTP e informações de cadeia de caracteres de consulta será removido.
- `ruleName` (obrigatório) - SAS nome da regra para qualquer entidade representada por determinado URI ou para o espaço de nomes representado por parte de anfitrião do URI.
- `key` (obrigatório) - chave válida para a regra SAS. 
- `expirationSeconds` (opcional) – o número de segundos até que o token gerado deve expirar. Se não for especificado, a predefinição é 1 hora (3600).

O token emitido confere direitos associados à regra SAS especificada durante o período especificado.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Este método é funcionalmente equivalente ao `createRelayToken` método documentado anteriormente, mas devolve o token de anexado corretamente para a URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe ws. RelayedServer

O `hycows.RelayedServer` classe é uma alternativa para o `ws.Server` classe que não escutam na rede local, mas delegados escuta para o serviço de reencaminhamento do Azure.

As duas classes são principalmente a compatível, o que significa que uma aplicação existente com o contrato a `ws.Server` classe pode ser alterada facilmente para utilizar a versão retransmitida. As principais diferenças são no construtor e nas opções disponíveis.

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

O `RelayedServer` construtor suporta um conjunto diferente de argumentos que o `Server`, porque não é um serviço de escuta do autónomo ou podem ser incorporados numa arquitetura de serviço de escuta HTTP existente. Também existem menos opções disponíveis, uma vez que a gestão de WebSocket em grande parte é delegada para o serviço de reencaminhamento.

Argumentos do construtor:

- `server` (obrigatório) - o URI completamente qualificado de um nome de ligação híbrida na qual pode escutar, e geralmente construída com o método de programa auxiliar de WebSocket.createRelayListenUri().
- `token` Este argumento (obrigatório) - contém uma cadeia de caracteres de token emitida anteriormente ou uma função de retorno de chamada que pode ser chamada para obter esse uma cadeia de token. A opção de chamada de retorno é preferencial, pois permitirá renovação token.

#### <a name="events"></a>Eventos

`RelayedServer` instâncias de emitem três eventos que permitem-lhe processar os pedidos recebidos, estabeleça ligações e detetar condições de erro. Tem de subscrever o `connect` eventos para processar mensagens. 

##### <a name="headers"></a>Cabeçalhos

```JavaScript 
function(headers)
```

O `headers` o evento é gerado antes de uma ligação recebida for aceita, permitindo que a modificação dos cabeçalhos para enviar para o cliente. 

##### <a name="connection"></a>ligação

```JavaScript
function(socket)
```

Emitida quando uma nova conexão WebSocket é aceite. O objeto é do tipo `ws.WebSocket`, tal como com o pacote básico.


##### <a name="error"></a>erro

```JavaScript
function(error)
```

Se o servidor subjacente emite um erro, ele é reencaminhado aqui.  

#### <a name="helpers"></a>Auxiliares

Para simplificar a partir de um servidor retransmitido e imediatamente subscrever as ligações de entrada, o pacote expõe uma função auxiliar simples, que também é usada nos exemplos, da seguinte forma:

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

Esse método chama o construtor para criar uma nova instância do RelayedServer e, em seguida, assina o retorno de chamada fornecido para o evento de "ligação".
 
##### <a name="relayedconnect"></a>relayedConnect

Espelhamento simplesmente a `createRelayedServer` auxiliar na função, `relayedConnect` cria uma ligação de cliente e subscreve o evento de "abrir" no soquete resultante.

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

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de reencaminhamento disponíveis](relay-api-overview.md)
