---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184846"
---
### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Criar um novo ficheiro JavaScript denominado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento

Execute `npm install hyco-ws` a partir de uma linha de comandos do Nó na sua pasta do projeto.

### <a name="write-some-code-to-send-messages"></a>Escrever alguns códigos para enviar mensagens

1. Adicione o seguinte `constants` à parte superior do ficheiro `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Adicione as seguintes constantes ao ficheiro `sender.js` para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores obtidos quando criou a ligação híbrida.
   
   1. `const ns` - O espaço de nomes do Reencaminhamento. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - O nome da ligação híbrida.
   3. `const keyrule` - O nome da chave SAS.
   4. `const key` - O valor da chave SAS.

3. Adicione o seguinte código ao ficheiro `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    O ficheiro sender.js deve ter o seguinte aspeto:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

