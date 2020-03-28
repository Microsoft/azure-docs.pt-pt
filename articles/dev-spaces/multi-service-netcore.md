---
title: 'Executar vários serviços dependentes: .NET Core & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar o Azure Dev Spaces e o Visual Studio Code para desbugificar uma aplicação multi-serviço .NET Core no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 0bbb1aefe517c45207160b83b89f7207e8909666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438305"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Executar vários serviços dependentes: .NET Core e Visual Studio Code com Espaços Azure Dev

Neste tutorial, você vai aprender a desenvolver aplicações multi-serviço usando espaços Azure Dev, juntamente com alguns dos benefícios adicionais que a Dev Spaces proporciona.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução num contentor separado

Nesta secção, você vai criar `mywebapi`um segundo `webfrontend` serviço, e tem que chamá-lo. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![Vários contentores](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Transfira o código de exemplo para *mywebapi*
Para ser mais rápido, vamos transferir código de exemplo de um repositório do GitHub. Aceda a https://github.com/Azure/dev-spaces e selecione **Clone or Download** (Clonar ou Transferir) para transferir o repositório do GitHub. O código para esta secção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra a pasta `mywebapi` numa *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Este comando não deve ser confundido com o comando `azds prep`, que configura o projeto para implementação.
1. Prima F5 e aguarde que o serviço seja criado e implementado. Saberá que está pronto quando a *candidatura começar. Pressione Ctrl+C para desligar.* mensagem aparece na consola de depuração.
1. O URL de ponto final será algo parecido com `http://localhost:<portnumber>`. **Dica: A barra de estado do Código VS ficará laranja e apresentará um URL clicável.** Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no nosso espaço de programador no Azure. O motivo para o endereço localhost é porque `mywebapi` não definiu quaisquer pontos finais públicos e apenas pode ser acedido a partir da instância do Kubernetes. Para sua comodidade e para facilitar a interação com o serviço privado da sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contentor em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra o browser no endereço localhost. Anexe `/api/values` ao URL para invocar a API GET predefinida para `ValuesController`.
1. Se todos os passos forem concluídos com êxito, deve conseguir ver uma resposta a partir do serviço `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Efetue um pedido de *webfrontend* para *mywebapi*
Vamos agora escrever código em `webfrontend` que efetua um pedido a `mywebapi`.
1. Mude para a janela do VS Code para `webfrontend`.
1. *Substitua* o código para `HomeController.cs`o método Sobre em:

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

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá mais tarde como isto ajuda equipas com [desenvolvimento colaborativo.](team-development-netcore.md)

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Detete um `Get(int id)` ponto de `api/values/{id}` rutura dentro do método que trata os pedidos GET. Isto é em torno da [linha 23 no ficheiro *Controladores/ValoresController.cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23).
1. No projeto `webfrontend`, defina um ponto de interrupção antes de ser enviado um pedido GET para `mywebapi/api/values`. Isto é em torno da linha 32 no ficheiro [ *Controladores/HomeController.cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs) que modificou na secção anterior.
1. Prima F5 no projeto `webfrontend`.
1. Invoque a aplicação Web e siga o código em ambos os serviços.
1. Na aplicação Web, a página About (Sobre) apresentará uma mensagem concatenada pelos dois serviços: "Hello from webfrontend and Hello from mywebapi".

### <a name="well-done"></a>Já está!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça o desenvolvimento de equipas em Espaços Dev](team-development-netcore.md)
