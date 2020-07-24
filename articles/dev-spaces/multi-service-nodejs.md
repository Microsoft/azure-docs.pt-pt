---
title: 'Executando múltiplos serviços dependentes: Node.js & Código do Estúdio Visual'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar Azure Dev Spaces e Visual Studio Code para depurar uma aplicação de Node.js multi-serviço no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 2c87dedda1db97a033526c809de735fe036120ef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006987"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Executando múltiplos serviços dependentes: Node.js e Código de Estúdio Visual com Azure Dev Spaces

Neste tutorial, você vai aprender a desenvolver aplicações multi-serviços usando Azure Dev Spaces, juntamente com alguns dos benefícios adicionais que a Dev Spaces proporciona.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução num contentor separado

Nesta secção, vai criar um segundo serviço, `mywebapi` a ser chamado por `webfrontend`. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![O diagrama mostra o serviço de frontalção web chamando (como indicado por uma seta) o serviço mywebapi.](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abra o código de exemplo para *mywebapi*
Já deve ter o código de amostra `mywebapi` para este guia sob uma pasta chamada `samples` (se não, vá e https://github.com/Azure/dev-spaces selecione **Clone ou Descarregue** para descarregar o repositório GitHub.) O código desta secção está em `samples/nodejs/getting-started/mywebapi` .

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra a pasta `mywebapi` numa *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Este comando não deve ser confundido com o comando `azds prep`, que configura o projeto para implementação.
1. Prima F5 e aguarde que o serviço seja criado e implementado. Saberá que está pronto quando a *mensagem "Ouvir" na porta 80* aparecer na consola de depurrão.
1. Tome nota do URL do ponto final, algo semelhante a `http://localhost:<portnumber>`. **Sugestão: A barra de estado do código VS ficará laranja e apresentará um URL clicável.** Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no nosso ambiente de desenvolvimento no Azure. O motivo para o endereço localhost é porque `mywebapi` não definiu quaisquer pontos finais públicos e apenas pode ser acedido a partir da instância do Kubernetes. Para sua comodidade e para facilitar a interação com o serviço privado da sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contentor em execução no Azure.
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
   1. *Remova* a linha `server.close()` no final de `server.js`

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá posteriormente como isto ajuda as equipas de desenvolvimento de colaboração.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Desaponte um ponto de rutura no interior do manipulador GET padrão `/` [na linha 8 de `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. No projeto `webfrontend`, defina um ponto de interrupção antes de ser enviado um pedido GET para `http://mywebapi`.
1. Prima F5 no projeto `webfrontend`.
1. Abra a aplicação Web e siga o código em ambos os serviços. A aplicação Web deve apresentar uma mensagem concatenada pelos dois serviços: "Hello from webfrontend and Hello from mywebapi."

### <a name="well-done"></a>Parabéns!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça o desenvolvimento de equipas em Dev Spaces](team-development-nodejs.md)
