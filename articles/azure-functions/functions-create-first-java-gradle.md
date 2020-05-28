---
title: Use Java e Gradle para publicar uma função no Azure
description: Crie e publique uma função desencadeada pelo HTTP para Azure com Java e Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: a366a199338539ba8e599bd5f406838f4e7bd21c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996653"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Use Java e Gradle para criar e publicar uma função para azure

Este artigo mostra-lhe como construir e publicar um projeto de função Java para funções Azure com a ferramenta de linha de comando Gradle. Quando terminar, o seu código de função funciona em Azure num plano de [hospedagem sem servidores](functions-scale.md#consumption-plan) e é desencadeado por um pedido http. 

> [!NOTE]
> Se Gradle não é a sua ferramenta de desenvolvimento preferencial, confira os nossos tutoriais semelhantes para desenvolvedores java usando [Maven,](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções com o Java, tem de ter o seguinte instalado:

- [Java Development Kit](https://aka.ms/azure-jdks), versão 8
- [CLI do Azure]
- [Funções Azure Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior
- [Gradle](https://gradle.org/), versão 4.10 e acima

Também precisa de uma subscrição azure ativa. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="prepare-a-functions-project"></a>Preparar um projeto funções

Utilize o seguinte comando para clonar o projeto da amostra:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Abra `build.gradle` e altere a `appName` secção seguinte para um nome único para evitar conflitos de nomes de domínio ao ser implantado para Azure. 

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

Abra o novo ficheiro Function.java a partir do caminho *src/main/java* num editor de texto e reveja o código gerado. Este código é uma função desencadeada em [HTTP](functions-bindings-http-webhook.md) que ecoa o corpo do pedido. 

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Executar localmente a função

Executar o seguinte comando para construir e executar o projeto de função:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Você vê saída como o seguinte das Ferramentas Core funções azure quando executa o projeto localmente:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Desencadear a função a partir da linha de comando utilizando o seguinte comando cURL numa nova janela de terminais:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A saída esperada é a seguinte:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Se definir authLevel para ou , a chave de `FUNCTION` `ADMIN` [função](functions-bindings-http-webhook-trigger.md#authorization-keys) não é necessária quando se executa localmente.  

Utilize `Ctrl+C` no terminal para parar o código de função.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

Uma aplicação de função e recursos relacionados são criados no Azure quando você implementa a sua aplicação de funções pela primeira vez. Antes de ser implementado, utilize o comando [az login](/cli/azure/authenticate-azure-cli) Azure CLI para iniciar sessão na sua subscrição Azure. 

```azurecli
az login
```

> [!TIP]
> Se a sua conta conseguir aceder a várias subscrições, utilize o [conjunto de conta Az](/cli/azure/account#az-account-set) para definir a subscrição predefinida para esta sessão. 

Utilize o seguinte comando para implementar o seu projeto numa nova aplicação de funções. 

```bash
gradle azureFunctionsDeploy
```

Isto cria os seguintes recursos em Azure, com base nos valores do ficheiro build.gradle:

+ Grupo de recursos. Nomeado com o _recursoGroup_ que forneceu.
+ Conta de armazenamento. Exigido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de Serviço de Aplicações. Plano de consumo sem servidor que acolhe para a sua aplicação de funções na app especificada _Região_. O nome é gerado aleatoriamente.
+ App de funções. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é o seu nome de _aplicação,_ anexado com um número gerado aleatoriamente. 

A implementação também embala os ficheiros do projeto e os implanta na nova aplicação de funções utilizando [a implementação](functions-deployment-technologies.md#zip-deploy)de zip , com o modo de execução do pacote ativado.

O authLevel para HTTP Trigger no projeto da amostra `ANONYMOUS` é, que irá ignorar a autenticação. No entanto, se utilizar outro authLevel como ou , precisa de obter a chave de `FUNCTION` `ADMIN` função para chamar o ponto final da função em HTTP. A maneira mais fácil de obter a chave de função é a partir do [portal Azure].

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obtenha o URL do gatilho HTTP

Pode obter o URL necessário para acionar a sua função, com a chave de função, a partir do portal Azure. 

1. Navegue no [portal Azure,]inscreva-se, escreva o nome da _sua_ aplicação de função em **Search** no topo da página e prima o enter.
 
1. Na sua aplicação de funções, selecione **Funções,** escolha a sua função e, em seguida, clique em **</> Obter Url** de Função na parte superior direita. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Copiar o URL da função a partir do portal do Azure":::

1. Escolha **o predefinido (Chave de Função)** e selecione **Copiar**. 

Agora pode utilizar o URL copiado para aceder à sua função.

## <a name="verify-the-function-in-azure"></a>Verifique a função em Azure

Para verificar a aplicação de funções em funcionamento no Azure `cURL` utilizando, substitua o URL da amostra abaixo com o URL que copiou do portal.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Isto envia um pedido de CORREIO para o ponto final da função com `AzureFunctions` no corpo do pedido. Vê a seguinte resposta.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Próximos passos

Criou um projeto de funções Java com uma função ativada http, executa-o na sua máquina local e implantou-o para o Azure. Agora, prolongue a sua função por...

> [!div class="nextstepaction"]
> [Adicionar uma ligação de saída de fila de armazenamento Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Portal do Azure]: https://portal.azure.com
