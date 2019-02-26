---
title: Executar vários serviços dependentes com node. js e o VS Code | Documentos da Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 8d305e051bd6708977926e3a4de47c15a784c7b6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818821"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Desenvolvimento múltiplos serviço com os espaços de desenvolvimento do Azure

Neste tutorial, irá aprender como desenvolver aplicativos múltiplos serviços com espaços de desenvolvimento do Azure, juntamente com alguns os benefícios adicionais, que fornece de espaços de desenvolvimento.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução num contentor separado

Nesta secção, vai criar um segundo serviço, `mywebapi` a ser chamado por `webfrontend`. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abra o código de exemplo para *mywebapi*
Já deverá ter o código de exemplo para `mywebapi` deste guia sob uma pasta denominada `samples` (caso contrário, aceda a https://github.com/Azure/dev-spaces e selecione **Clonar ou Transferir** para transferir o repositório do GitHub.) O código para esta secção está em `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra a pasta `mywebapi` numa *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Este comando não deve ser confundido com o comando `azds prep`, que configura o projeto para implementação.
1. Prima F5 e aguarde que o serviço seja criado e implementado. Saberá que está pronto quando for apresentada a barra de depuração do VS Code.
1. Tome nota do URL do ponto final, algo semelhante a http://localhost:\<portnumber\>. **Sugestão: A barra de status do VS Code irá apresentar um URL clicável.** Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no nosso ambiente de desenvolvimento no Azure. O motivo para o endereço localhost é porque `mywebapi` não definiu quaisquer pontos finais públicos e apenas pode ser acedido a partir da instância do Kubernetes. Para sua comodidade e para facilitar a interação com o serviço privado da sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contentor em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra o browser no endereço localhost. Deverá ver uma resposta a partir do serviço `mywebapi` ("Hello from mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Efetue um pedido de *webfrontend* para *mywebapi*
Vamos agora escrever código em `webfrontend` que efetua um pedido a `mywebapi`.
1. Mude para a janela do VS Code para `webfrontend`.
1. Adicione estas linhas de código no topo de `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Substitua* o código para o processador GET `/api`. Ao processar um pedido, ele faz uma chamada para `mywebapi` e, em seguida, devolve os resultados de ambos os serviços.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
 4. *Remova* a linha `server.close()` no final de `server.js`

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá posteriormente como isto ajuda as equipas de desenvolvimento de colaboração.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no processador GET `/` predefinido.
1. No projeto `webfrontend`, defina um ponto de interrupção antes de ser enviado um pedido GET para `http://mywebapi`.
1. Prima F5 no projeto `webfrontend`.
1. Abra a aplicação Web e siga o código em ambos os serviços. A aplicação web deve exibir uma mensagem concatenada por dois serviços: "Olá de webfrontend e Hello do mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>Rastreamento de automática para mensagens de HTTP
Talvez tenha notado que, embora *webfrontend* não contém qualquer código especial para imprimir a chamada HTTP faz ao *mywebapi*, pode ver as mensagens na janela de saída de rastreios de HTTP:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Este é um dos benefícios "gratuitos" que obtém da instrumentação de espaços de desenvolvimento. Inserimos componentes que monitorizam os pedidos de HTTP, à medida que passam pelo sistema para que seja mais fácil para controlar as chamadas de múltiplos serviços complexas durante o desenvolvimento.

### <a name="well-done"></a>Já está!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe nos espaços de desenvolvimento](team-development-nodejs.md)