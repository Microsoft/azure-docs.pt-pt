---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184849"
---
### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Criar um novo ficheiro JavaScript denominado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento

Execute `npm install hyco-ws` a partir de uma linha de comandos do Nó na sua pasta do projeto.

### <a name="write-some-code-to-receive-messages"></a>Escrever alguns códigos para receber mensagens

1. Adicione a seguinte constante à parte superior do ficheiro `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Adicione as seguintes constantes ao ficheiro `listener.js` para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores obtidos quando criou a ligação híbrida.
   
   1. `const ns` - O espaço de nomes do Reencaminhamento. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - O nome da ligação híbrida.
   3. `const keyrule` - O nome da chave SAS.
   4. `const key` - O valor da chave SAS.

3. Adicione o seguinte código ao ficheiro `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    O ficheiro listener.js deve ter o seguinte aspeto:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

