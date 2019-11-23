---
title: 'Running multiple dependent services: Java & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 3fe19997ab54f02b6a5f029abbdb69d5ea6532f7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325718"
---
# <a name="running-multiple-dependent-services-java-and-visual-studio-code-with-azure-dev-spaces"></a>Running multiple dependent services: Java and Visual Studio Code with Azure Dev Spaces

In this tutorial, you'll learn how to develop multi-service applications using Azure Dev Spaces, along with some of the added benefits that Dev Spaces provides.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução num contentor separado

Nesta secção, vai criar um segundo serviço, `mywebapi` a ser chamado por `webfrontend`. Cada serviço vai ser executado em contentores separados. Em seguida, vai realizar a depuração em ambos os contentores.

![Vários contentores](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Transfira o código de exemplo para *mywebapi*
Para ser mais rápido, vamos transferir código de exemplo de um repositório do GitHub. Aceda a https://github.com/Azure/dev-spaces e selecione **Clone or Download** (Clonar ou Transferir) para transferir o repositório do GitHub. O código para esta secção está em `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra a pasta `mywebapi` numa *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Prima F5 e aguarde que o serviço seja criado e implementado. You'll know it's ready when a message similar to the below appears in the debug console:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. O URL de ponto final será algo parecido com `http://localhost:<portnumber>`. **Tip: The VS Code status bar will turn orange and display a clickable URL.** Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no nosso espaço de programador no Azure. O motivo para o endereço localhost é porque `mywebapi` não definiu quaisquer pontos finais públicos e apenas pode ser acedido a partir da instância do Kubernetes. Para sua comodidade e para facilitar a interação com o serviço privado da sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contentor em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra o browser no endereço localhost.
1. Se todos os passos forem concluídos com êxito, deve conseguir ver uma resposta a partir do serviço `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Efetue um pedido de *webfrontend* para *mywebapi*
Vamos agora escrever código em `webfrontend` que efetua um pedido a `mywebapi`.
1. Mude para a janela do VS Code para `webfrontend`.
1. *Adicione* as seguintes instruções `import` sob a instrução `package`:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Substitua* pelo código para o método de saudação:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

O exemplo de código anterior reencaminha o cabeçalho `azds-route-as` do pedido a receber para o pedido a enviar. Verá posteriormente como isto ajuda as equipas de desenvolvimento de colaboração.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste momento, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não for esse o caso, prima F5 no projeto `mywebapi`.
1. Set a breakpoint in the `index()` method of the `mywebapi` project, on [line 19 of `Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java#L19)
1. No projeto `webfrontend`, defina um ponto de interrupção antes de ser enviado um pedido GET para `mywebapi`, na linha que começa por `try`.
1. Prima F5 no projeto `webfrontend` (ou reinicie o depurador se estiver atualmente em execução).
1. Invoque a aplicação Web e siga o código em ambos os serviços.
1. Na aplicação Web, a página About (Sobre) apresentará uma mensagem concatenada pelos dois serviços: "Hello from webfrontend and Hello from mywebapi".

### <a name="well-done"></a>Já está!
Tem agora uma aplicação com vários contentores, na qual cada contentor pode ser desenvolvido e implementado separadamente.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Learn about team development in Dev Spaces](team-development-java.md)
