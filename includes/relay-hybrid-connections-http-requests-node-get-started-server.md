---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184496"
---
### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Criar um novo ficheiro JavaScript denominado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento

Execute `npm install hyco-https` a partir de uma linha de comandos do Nó na sua pasta do projeto.

### <a name="write-some-code-to-handle-requests"></a>Escreva algum código para processar pedidos

1. Adicione a seguinte constante à parte superior do ficheiro `listener.js`.

    ```js
    const https = require('hyco-https');
    ```
2. Adicione as seguintes constantes ao ficheiro `listener.js` para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores obtidos quando criou a ligação híbrida.

   1. `const ns` - O espaço de nomes do Reencaminhamento. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - O nome da ligação híbrida.
   3. `const keyrule` - O nome da chave SAS.
   4. `const key` - O valor da chave SAS.

3. Adicione o seguinte código ao ficheiro `listener.js`. :

    Vai reparar que o código não é muito diferente de qualquer exemplo de servidor HTTP simples que pode encontrar nos tutoriais iniciantes do Node.js com a exceção de utilizar `createRelayedServer` em vez da típica função `createServer`.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    O ficheiro listener.js deve ter o seguinte aspeto:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

