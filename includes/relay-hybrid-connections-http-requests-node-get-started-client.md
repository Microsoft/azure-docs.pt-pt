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
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184497"
---
### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Se tiver desativado a opção "Requer Autorização do Cliente" ao criar o Reencaminhamento, pode enviar os pedidos para o URL de Ligações Híbridas com qualquer browser. Para aceder a pontos finais protegidos, tem de criar e transmitir um token no cabeçalho `ServiceBusAuthorization`, mostrado aqui.

Para começar, crie um novo ficheiro JavaScript denominado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento

Execute `npm install hyco-https` a partir de uma linha de comandos do Nó na sua pasta do projeto. Este pacote também importa o pacote regular `https`. Para o lado do cliente, a principal diferença é que o pacote fornece funções para construir tokens e URIs de Reencaminhamento.

### <a name="write-some-code-to-send-messages"></a>Escrever alguns códigos para enviar mensagens

1. Adicione o seguinte `constants` à parte superior do ficheiro `sender.js`.
   
    ```js
    const https = require('hyco-https');
    ```

2. Adicione as seguintes constantes ao ficheiro `sender.js` para obter os detalhes da ligação híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores obtidos quando criou a ligação híbrida.
   
   1. `const ns` - O espaço de nomes do Reencaminhamento. Certifique-se de que utiliza o nome de espaço de nomes totalmente qualificado, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - O nome da ligação híbrida.
   3. `const keyrule` - O nome da chave SAS.
   4. `const key` - O valor da chave SAS.

3. Adicione o seguinte código ao ficheiro `sender.js`. Vai notar que o código não difere significativamente da utilização normal do cliente HTTPS de Node.js; apenas adiciona o cabeçalho de autorização.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    O ficheiro sender.js deve ter o seguinte aspeto:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

