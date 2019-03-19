---
title: Executar vários serviços dependentes com .NET Core e o Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
ms.openlocfilehash: e302a4edc54b98f0dd731f65d0d45aa592cc72ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076773"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Desenvolvimento múltiplos serviço com os espaços de desenvolvimento do Azure

Neste tutorial, irá aprender como desenvolver aplicativos múltiplos serviços com espaços de desenvolvimento do Azure, juntamente com alguns os benefícios adicionais, que fornece de espaços de desenvolvimento.

## <a name="call-another-container"></a>Chamar outro contentor
Nesta secção, vai criar um segundo serviço `mywebapi`e ter `webfrontend` chamá-lo. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Transfira o código de exemplo para *mywebapi*
Para ser mais rápido, vamos transferir código de exemplo de um repositório do GitHub. Aceda a https://github.com/Azure/dev-spaces e selecione **Clone or Download** (Clonar ou Transferir) para transferir o repositório do GitHub. O código para esta secção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra o projeto `mywebapi` numa *janela separada do Visual Studio*.
1. Selecione **Azure Dev Spaces** no menu pendente de definições de início, tal como fez anteriormente para o projeto `webfrontend`. Agora, em vez de criar um novo cluster do AKS, selecione o mesmo que já criou. Tal como antes, mantenha a predefinição `default` em Space (Espaço) e clique em **OK**. Na janela de saída, pode perceber é iniciado do Visual Studio para "entender" este novo serviço no seu espaço de desenvolvimento para acelerar as coisas quando iniciar a depuração.
1. Prima F5 e aguarde que o serviço seja criado e implementado. Saberá que está pronto quando a barra de estado do Visual Studio ficar cor de laranja
1. Anote o URL apresentado no ponto final da **espaços de desenvolvimento do Azure para o AKS** painel na **saída** janela. Terá um aspeto semelhante a `http://localhost:<portnumber>`. Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no espaço de programador no Azure.
2. Quando o projeto `mywebapi` estiver pronto, abra o browser no endereço localhost e acrescente `/api/values` ao URL para invocar a API GET predefinida para `ValuesController`. 
3. Se todos os passos tiverem sido concluídos com êxito, deverá conseguir ver uma resposta do serviço `mywebapi` com um aspeto semelhante ao seguinte.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Efetue um pedido de *webfrontend* para *mywebapi*
Vamos agora escrever código em `webfrontend` que efetua um pedido a `mywebapi`. Mude para a janela do Visual Studio que tem o projeto `webfrontend`. Na `HomeController.cs` arquivo, *substituir* o código para o método About com o código a seguir:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá posteriormente como isso facilita uma experiência de desenvolvimento mais produtiva no [cenários de equipa](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no método `Get(int id)` no ficheiro `Controllers/ValuesController.cs` que processa os pedidos GET `api/values/{id}`.
1. No projeto `webfrontend` onde colou o código acima indicado, defina um ponto de interrupção antes de ser enviado um pedido GET para `mywebapi/api/values`.
1. Prima F5 no projeto `webfrontend`. O Visual Studio irá abrir novamente um browser para a porta localhost adequada e a aplicação Web será apresentada.
1. Clique na ligação "**About**" (Acerca de) na parte superior da página para acionar o ponto de interrupção no projeto `webfrontend`. 
1. Prima F10 para continuar. O ponto de interrupção no projeto `mywebapi` é acionado.
1. Prima F5 para continuar. Isso fará com que regresse ao código no projeto `webfrontend`.
1. Se premir F5 uma vez mais, o pedido será concluído e será devolvida uma página no browser. Na aplicação web, a página sobre apresenta uma mensagem de concatenado por dois serviços: "Olá de webfrontend e Hello do mywebapi."

Já está! Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.

### <a name="automatic-tracing-for-http-messages"></a>Rastreamento de automática para mensagens de HTTP
Talvez tenha notado que, embora *webfrontend* não contém qualquer código especial para imprimir a chamada HTTP faz ao *mywebapi*, pode ver as mensagens na janela de saída de rastreios de HTTP:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Este é um dos benefícios "gratuitos" que obtém da instrumentação de espaços de desenvolvimento. Inserimos componentes que monitorizam os pedidos de HTTP, à medida que passam pelo sistema para que seja mais fácil para controlar as chamadas de múltiplos serviços complexas durante o desenvolvimento.

### <a name="well-done"></a>Já está!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe nos espaços de desenvolvimento](team-development-netcore-visualstudio.md)