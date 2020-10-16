---
title: Use Java e Gradle para publicar uma função para Azure
description: Crie e publique uma função desencadeada por HTTP para Azure com Java e Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: ce2169838faaee8da459f80979fa31318e9d50c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322181"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Use Java e Gradle para criar e publicar uma função para Azure

Este artigo mostra-lhe como construir e publicar um projeto de função Java para funções Azure com a ferramenta de linha de comando Gradle. Quando termina, o seu código de função funciona em Azure num [plano de hospedagem sem servidor](functions-scale.md#consumption-plan) e é desencadeado por um pedido HTTP. 

> [!NOTE]
> Se gradle não é a sua ferramenta de desenvolvimento preferencial, confira os nossos tutoriais semelhantes para desenvolvedores java usando [Maven,](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [VS Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções com o Java, tem de ter o seguinte instalado:

- [Java Development Kit](https://aka.ms/azure-jdks), versão 8
- [CLI do Azure]
- [Azure Funções Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior
- [Gradle](https://gradle.org/), versão 4.10 ou superior

Também precisa de uma subscrição ativa do Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="prepare-a-functions-project"></a>Preparar um projeto de Funções

Utilize o seguinte comando para clonar o projeto da amostra:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Abra `build.gradle` e altere `appName` a secção seguinte para um nome único para evitar conflitos de nomes de domínio ao ser implantado em Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Abra o novo ficheiro Function.java a partir do caminho *src/main/java* num editor de texto e reveja o código gerado. Este código é uma função [desencadeada HTTP](functions-bindings-http-webhook.md) que ecoa o corpo do pedido. 

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Executar a função localmente

Executar o seguinte comando para construir e executar o projeto de função:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Você vê a saída como o seguinte de Azure Functions Core Tools quando você executar o projeto localmente:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Despoletou a função a partir da linha de comando utilizando o seguinte comando cURL numa nova janela de terminal:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A saída prevista é a seguinte:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Se definir authLevel `FUNCTION` `ADMIN` ou, a [tecla de função](functions-bindings-http-webhook-trigger.md#authorization-keys) não é necessária quando funciona localmente.  

Utilize `Ctrl+C` no terminal para parar o código de função.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

Uma aplicação de função e recursos relacionados são criados no Azure quando implementa a sua aplicação de função pela primeira vez. Antes de poder implementar, utilize o comando [Azure](/cli/azure/authenticate-azure-cli) CLI do login para iniciar sessão na sua subscrição Azure. 

```azurecli
az login
```

> [!TIP]
> Se a sua conta puder aceder a várias subscrições, utilize [a conta AZ definida](/cli/azure/account#az-account-set) para definir a subscrição padrão para esta sessão. 

Utilize o seguinte comando para implantar o seu projeto numa nova aplicação de função. 

```bash
gradle azureFunctionsDeploy
```

Isto cria os seguintes recursos em Azure, com base nos valores no ficheiro build.gradle:

+ Grupo de recursos. Nomeado com o _grupo de recursos_ que forneceu.
+ Conta de armazenamento. Requerido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de Serviço de Aplicações. Plano de consumo sem servidor hospedado para a sua aplicação de função na _aplicação especificadaRegion_. O nome é gerado aleatoriamente.
+ App de função. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é o seu _nome de aplicação,_ anexado com um número gerado aleatoriamente. 

A implementação também embala os ficheiros do projeto e implementa-os na nova aplicação de função utilizando a [implementação zip](functions-deployment-technologies.md#zip-deploy), com modo de funcionamento do pacote ativado.

O authLevel for HTTP Trigger no projeto de amostra é `ANONYMOUS` , que irá saltar a autenticação. No entanto, se utilizar outros authLevel like `FUNCTION` ou , precisa de obter a `ADMIN` tecla de função para ligar para o ponto final da função em HTTP. A forma mais fácil de obter a chave de função é a partir do [portal Azure].

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obtenha o URL do gatilho HTTP

Pode obter o URL necessário para ativar a sua função, com a tecla de função, a partir do portal Azure. 

1. Navegue pelo [portal Azure,]inscreva-se, digite a _appName_ da sua aplicação de função em **Procurar** no topo da página e prima para introduzir.
 
1. Na sua aplicação de funções, selecione **Funções,** escolha a sua função e, em seguida, clique ** em</> Obtenha** o Url de função no topo direito. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Copiar o URL da função a partir do portal do Azure":::

1. Escolha **o predefinido (Tecla função)** e selecione **Copy**. 

Agora pode utilizar o URL copiado para aceder à sua função.

## <a name="verify-the-function-in-azure"></a>Verifique a função em Azure

Para verificar a aplicação de função em execução no Azure `cURL` utilizando, substitua o URL da amostra abaixo pelo URL que copiou do portal.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Isto envia um pedido DEM para o ponto final da função com `AzureFunctions` no corpo do pedido. Vê a seguinte resposta.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Passos seguintes

Criou um projeto de funções Java com uma função httpizada, executou-o na sua máquina local e implantou-o para Azure. Agora, aumente a sua função por...

> [!div class="nextstepaction"]
> [Adicionar uma ligação de saída de fila de armazenamento Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Portal do Azure]: https://portal.azure.com
