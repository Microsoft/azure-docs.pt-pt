---
title: Visão geral das APIs do Nó de Relé Azure Microsoft Docs
description: Este artigo fornece uma visão geral da API Node.js para o serviço Azure Relay. Também mostra como usar o pacote hyco-ws Node.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263492"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Visão geral da API do relé híbrido de ligações híbridas

## <a name="overview"></a>Descrição geral

O [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) pacote de nó para ligações híbridas Azure Relay é construído e estende o pacote [NPM 'ws'.](https://www.npmjs.com/package/ws) Este pacote reenca exporta todas as exportações desse pacote base e adiciona novas exportações que permitem a integração com o recurso Azure Relay Hybrid Connections. 

Aplicações existentes que `require('ws')` podem usar este pacote `require('hyco-ws')` com, em vez disso, o que também permite cenários híbridos em que uma aplicação pode ouvir as ligações WebSocket localmente a partir de "dentro da firewall" e via Conexões Híbridas, tudo ao mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs estão [documentadas no pacote principal 'ws'.](https://github.com/websockets/ws/blob/master/doc/ws.md) Este artigo descreve como este pacote difere dessa linha de base. 

As principais diferenças entre o pacote base e este 'hyco-ws' é que adiciona uma nova classe de servidor, exportada via `require('hyco-ws').RelayedServer` , e alguns métodos de ajuda.

### <a name="package-helper-methods"></a>Métodos de ajuda ao pacote

Existem vários métodos de utilidade disponíveis na exportação de pacotes que pode referir da seguinte forma:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos de ajuda são para uso com este pacote, mas também podem ser usados por um servidor Nó para permitir que clientes web ou dispositivos criem ouvintes ou remetentes. O servidor utiliza estes métodos passando-lhes URIs que incorporam fichas de curta duração. Estes URIs também podem ser usados com pilhas webSocket comuns que não suportam a definição de cabeçalhos HTTP para o aperto de mão WebSocket. A incorporação de fichas de autorização no URI é suportada principalmente para os cenários de utilização externos da biblioteca. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria um URI de ligação híbrido Azure Relay válido para o espaço e caminho de nome dado. Este URI pode então ser usado com a versão de retransmissão da classe WebSocketServer.

- `namespaceName` (obrigatório) - o nome qualificado para o domínio do espaço de identificação Azure Relay para utilizar.
- `path` (obrigatório) - o nome de uma conexão híbrida Azure Relay existente nesse espaço de nome.
- `token` (opcional) - um token de acesso retransmissor previamente emitido que está incorporado no URI do ouvinte (ver exemplo a seguir).
- `id` (opcional) - um identificador de rastreio que permite o rastreio dos pedidos de diagnóstico de ponta a ponta.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP juntamente com o aperto de mão WebSocket, como é o caso da stack W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>criar RelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria uma conexão híbrida Azure Relay válida enviar URI para o espaço e caminho de nome dado. Este URI pode ser usado com qualquer cliente WebSocket.

- `namespaceName` (obrigatório) - o nome qualificado para o domínio do espaço de identificação Azure Relay para utilizar.
- `path` (obrigatório) - o nome de uma conexão híbrida Azure Relay existente nesse espaço de nome.
- `token` (opcional) - um token de acesso retransmissor previamente emitido que está incorporado no URI de envio (ver exemplo a seguir).
- `id` (opcional) - um identificador de rastreio que permite o rastreio dos pedidos de diagnóstico de ponta a ponta.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar cabeçalhos HTTP juntamente com o aperto de mão WebSocket, como é o caso da stack W3C WebSocket.                   


#### <a name="createrelaytoken"></a>criarRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um token de assinatura de acesso compartilhado Azure Relay (SAS) para a regra URI, regra SAS e SAS que é válida para o número de segundos dado ou por uma hora a partir do instante atual se o argumento de expiração for omitido.

- `uri` (obrigatório) - o URI para o qual o símbolo deve ser emitido. O URI é normalizado para usar o esquema HTTP, e a informação de cadeia de consulta é despojada.
- `ruleName` (obrigatório) - Nome da regra SAS para a entidade representada pelo URI dado, ou para o espaço de nome representado pela parte de anfitrião URI.
- `key` (obrigatório) - chave válida para a regra SAS. 
- `expirationSeconds` (opcional) - o número de segundos até ao termo do token gerado. Se não for especificado, o predefinido é de 1 hora (3600).

O token emitido confere os direitos associados à regra SAS especificada durante a duração determinada.

#### <a name="appendrelaytoken"></a>apêndiceRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Este método é funcionalmente equivalente ao `createRelayToken` método documentado anteriormente, mas devolve o token corretamente anexado ao URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe Ws. Server Retransmitido

A `hycows.RelayedServer` classe é uma alternativa à classe que não ouve na rede `ws.Server` local, mas delega ouvir o serviço Azure Relay.

As duas classes são maioritariamente compatíveis com contratos, o que significa que uma aplicação existente que usa a `ws.Server` classe pode facilmente ser alterada para usar a versão retransmitida. As principais diferenças estão no construtor e nas opções disponíveis.

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

O `RelayedServer` construtor suporta um conjunto diferente de argumentos que o , porque não é um ouvinte `Server` autónomo, ou pode ser incorporado num quadro de ouvintes HTTP existente. Há também menos opções disponíveis uma vez que a gestão WebSocket é delegada em grande parte no serviço Relay.

Argumentos construtores:

- `server` (obrigatório) - o URI totalmente qualificado para um nome de ligação híbrida no qual ouvir, geralmente construído com o método de ajuda WebSocket.createRelayListenUri().
- `token` (obrigatório) - este argumento detém uma corda simbólica previamente emitida ou uma função de retorno que pode ser chamada para obter tal corda simbólica. A opção de retorno é preferida, pois permite a renovação do sinal.

#### <a name="events"></a>Eventos

`RelayedServer` casos emitem três eventos que lhe permitem lidar com pedidos de entrada, estabelecer ligações e detetar condições de erro. Tem de subscrever o `connect` evento para lidar com mensagens. 

##### <a name="headers"></a>cabeçalhos

```JavaScript 
function(headers)
```

O `headers` evento é levantado pouco antes de uma ligação recebida ser aceite, permitindo a modificação dos cabeçalhos para enviar ao cliente. 

##### <a name="connection"></a>ligação

```JavaScript
function(socket)
```

Emitida quando uma nova ligação WebSocket é aceite. O objeto é do tipo `ws.WebSocket` , tal como com a embalagem base.


##### <a name="error"></a>erro

```JavaScript
function(error)
```

Se o servidor subjacente emitir um erro, é reencaminhado para cá.  

#### <a name="helpers"></a>Ajudantes

Para simplificar o início de um servidor retransmitido e subscrever imediatamente as ligações recebidas, o pacote expõe uma função de ajudante simples, que também é usada nos exemplos, da seguinte forma:

##### <a name="createrelayedlistener"></a>criar Reformador Reformador

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

##### <a name="createrelayedserver"></a>criar RelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Este método chama o construtor a criar uma nova instância do RelayedServer e, em seguida, subscreve a chamada fornecida para o evento de "conexão".
 
##### <a name="relayedconnect"></a>retransmitidoConnect

Simplesmente espelhando o `createRelayedServer` ajudante em função, `relayedConnect` cria uma ligação ao cliente e subscreve o evento 'aberto' na tomada resultante.

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
* [O que é o Azure Relay?](relay-what-is-it.md)
* [APIs de retransmissão disponível](relay-api-overview.md)
