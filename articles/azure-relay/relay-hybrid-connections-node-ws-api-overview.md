---
title: Visão geral do Nó de Relé Azure APIs [ Microsoft Docs
description: Este artigo fornece uma visão geral da API node.js para o serviço Azure Relay. Também mostra como usar o pacote de nó de hyco-ws.
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
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211843"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Visão geral do nó de conexões híbridas retransmissor

## <a name="overview"></a>Descrição geral

O [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) pacote nó para ligações híbridas De retransmissão Azure é construído e estende o pacote NPM ['ws'.](https://www.npmjs.com/package/ws) Este pacote reexporta todas as exportações desse pacote base e adiciona novas exportações que permitem a integração com a funcionalidade Ligações Híbridas do serviço Azure Relay. 

Aplicações existentes que `require('ws')` podem utilizar este pacote `require('hyco-ws')` com, em vez disso, que também permite cenários híbridos em que uma aplicação pode ouvir ligações WebSocket localmente a partir de "dentro da firewall" e via Ligações Híbridas, tudo ao mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs estão [documentadas no pacote principal 'ws'.](https://github.com/websockets/ws/blob/master/doc/ws.md) Este artigo descreve como este pacote difere dessa linha de base. 

As principais diferenças entre o pacote base e este 'hyco-ws' é que adiciona uma nova classe de servidor, exportada via `require('hyco-ws').RelayedServer` , e alguns métodos de ajuda.

### <a name="package-helper-methods"></a>Métodos de ajuda de pacote

Existem vários métodos de utilidade disponíveis na exportação do pacote que pode referir da seguinte forma:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos de ajuda são para uso com este pacote, mas também podem ser usados por um servidor do Nó para permitir que os clientes web ou dispositivos criem ouvintes ou remetentes. O servidor usa estes métodos passando-os por URIs que incorporavam fichas de curta duração. Estes URIs também podem ser usados com pilhas comuns do WebSocket que não suportam a definição de cabeçalhos HTTP para o aperto de mão WebSocket. Incorporar fichas de autorização no URI é suportado principalmente para os cenários de utilização externos da biblioteca. 

#### <a name="createrelaylistenuri"></a>criarRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria um ouvinte de ligação híbrida azure relay válido URI para o espaço e caminho do nome dado. Este URI pode então ser utilizado com a versão de retransmissão da classe WebSocketServer.

- `namespaceName`(obrigatório) - o nome qualificado por domínio do espaço de nome do Relé Azure a utilizar.
- `path`(necessário) - o nome de uma ligação híbrida azure relé existente nesse espaço de nome.
- `token`(opcional) - um sinal de acesso relé previamente emitido que está incorporado no ouvinte URI (ver o seguinte exemplo).
- `id`(opcional) - um identificador de rastreio que permite o rastreio de diagnósticos de ponta a ponta dos pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP juntamente com o aperto de mão WebSocket, como é o caso da pilha W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>criarRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria uma conexão híbrida azure relay válida enviar URI para o espaço e caminho do nome dado. Este URI pode ser usado com qualquer cliente WebSocket.

- `namespaceName`(obrigatório) - o nome qualificado por domínio do espaço de nome do Relé Azure a utilizar.
- `path`(necessário) - o nome de uma ligação híbrida azure relé existente nesse espaço de nome.
- `token`(opcional) - um sinal de acesso relé previamente emitido que está incorporado no envio URI (ver o seguinte exemplo).
- `id`(opcional) - um identificador de rastreio que permite o rastreio de diagnósticos de ponta a ponta dos pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP juntamente com o aperto de mão WebSocket, como é o caso da pilha W3C WebSocket.                   


#### <a name="createrelaytoken"></a>criarRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um símbolo de assinatura de acesso partilhado de retransmissão azure (SAS) para a regra do alvo em causa URI, SAS e chave de regra SAS que é válida para o número determinado de segundos ou durante uma hora a partir do instante atual se o argumento de validade for omitido.

- `uri`(obrigatório) - o URI para o qual o símbolo deve ser emitido. O URI é normalizado para usar o esquema HTTP, e a informação de corda de consulta é despojada.
- `ruleName`(obrigatório) - Nome da regra SAS para a entidade representada pelo URI dado, ou para o espaço de nome representado pela parte do hospedeiro URI.
- `key`(obrigatório) - chave válida para a regra SAS. 
- `expirationSeconds`(opcional) - o número de segundos até que o token gerado expire. Se não especificado, o predefinido é de 1 hora (3600).

O token emitido confere os direitos associados à regra relativa às SAS especificadas durante a duração dada.

#### <a name="appendrelaytoken"></a>apêndiceRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Este método é funcionalmente equivalente ao `createRelayToken` método documentado anteriormente, mas devolve o token corretamente anexado ao URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe ws. Retransmissão Do Servidor

A `hycows.RelayedServer` classe é uma alternativa à classe que não ouve na rede `ws.Server` local, mas delega a ouvir o serviço Azure Relay.

As duas classes são maioritariamente compatíveis com contratos, o que significa que uma aplicação existente usando a `ws.Server` classe pode facilmente ser alterada para usar a versão retransmitida. As principais diferenças estão no construtor e nas opções disponíveis.

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

O `RelayedServer` construtor suporta um conjunto diferente de argumentos do que o, porque não é um ouvinte autónomo, ou capaz de ser incorporado num quadro de `Server` ouvintes HTTP existente. Existem também menos opções disponíveis, uma vez que a gestão webSocket é amplamente delegada no serviço Relay.

Argumentos de construtores:

- `server`(necessário) - o URI totalmente qualificado para um nome de ligação híbrida em que ouvir, geralmente construído com o método de ajudante WebSocket.createRelayListenUri().
- `token`(necessário) - este argumento contém uma corda simbólica previamente emitida ou uma função de chamada que pode ser chamada para obter tal corda simbólica. A opção de backback é preferível, pois permite a renovação simbólica.

#### <a name="events"></a>Eventos

`RelayedServer`casos emitem três eventos que lhe permitem lidar com pedidos de entrada, estabelecer ligações e detetar condições de erro. Deve subscrever o `connect` evento para lidar com mensagens. 

##### <a name="headers"></a>cabeçalhos

```JavaScript 
function(headers)
```

O evento é levantado pouco antes de `headers` uma ligação de entrada ser aceite, permitindo a modificação dos cabeçalhos para enviar ao cliente. 

##### <a name="connection"></a>ligação

```JavaScript
function(socket)
```

Emitido quando uma nova ligação WebSocket é aceite. O objeto é de `ws.WebSocket` tipo, tal como com o pacote base.


##### <a name="error"></a>erro

```JavaScript
function(error)
```

Se o servidor subjacente emitir um erro, é reencaminhado para cá.  

#### <a name="helpers"></a>Ajudantes

Para simplificar o início de um servidor retransmitido e subscrever imediatamente as ligações de entrada, o pacote expõe uma função simples de ajudante, que também é usada nos exemplos, da seguinte forma:

##### <a name="createrelayedlistener"></a>criarRelayedListener

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

##### <a name="createrelayedserver"></a>criarRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Este método chama o construtor para criar uma nova instância do RelayedServer e, em seguida, subscreve a chamada fornecida para o evento de "conexão".
 
##### <a name="relayedconnect"></a>relayedConnect

Simplesmente espelhar o `createRelayedServer` ajudante em função, `relayedConnect` cria uma ligação ao cliente e subscreve o evento 'aberto' na tomada resultante.

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
Para saber mais sobre o Azure Relay, visite estes links:
* [O que é Azure Relay?](relay-what-is-it.md)
* [APIs de retransmissão disponível](relay-api-overview.md)
