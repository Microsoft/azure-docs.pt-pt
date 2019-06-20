---
title: Criar a sua primeira função no Azure com o Java e o Maven | Microsoft Docs
description: Crie e publique uma função acionada por HTTP simples no Azure com o Java e o Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 1987bd348bf37c147eeb0697c88653c48f8c9136
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144204"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Criar a primeira função com o Java e Maven

Este artigo orienta-o através da utilização da ferramenta de linha de comando Maven para criar e publicar uma função de Java para as funções do Azure. Quando tiver terminado, o código de função é executado o [plano de consumo](functions-scale.md#consumption-plan) no Azure e pode ser acionada com um pedido HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver as funções com Java, tem de ter o seguinte instalado:

- [Java Development Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [As ferramentas de núcleo das funções do Azure](./functions-run-local.md#v2) versão 2.6.666 ou superior

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto das Funções

Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Se estiver a ter problemas com a execução do comando, dê uma olhada no que `maven-archetype-plugin` é utilizada a versão. Como está a executar o comando num diretório vazio sem qualquer `.pom` arquivo, ele pode tentar utilizar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` se tiver atualizado do Maven a partir de uma versão mais antiga. Nesse caso, tente eliminar o `maven-archetype-plugin` directory e voltar a executar o comando.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

O Maven pede-lhe os valores necessários para terminar de gerar o projeto. Para os valores de _groupId_, _artifactId_ e _versão_, veja a referência [Maven naming conventions (Convenções de nomenclatura do Maven)](https://maven.apache.org/guides/mini/guide-naming-conventions.html). O valor de _appName_ tem de ser exclusivo em todo o Azure, para que o Maven gere um nome de aplicação com base no _artifactId_ anteriormente introduzido por predefinição. O valor de _packageName_ determina o pacote de Java para o código de função gerado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são utilizados como exemplo e para facilitar a leitura dos passos posteriores deste início rápido. É encorajado a indicar os seus próprios valores para o Maven neste passo.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

O Maven cria os ficheiros de projeto numa pasta nova, com o nome _artifactId_, `fabrikam-functions` neste exemplo. Preparado para executar o código gerado no projeto é uma [acionada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) função que ecoa o corpo do pedido:

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="reference-bindings"></a>Enlaces de referência

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Executar localmente a função

Altere o diretório para a pasta do projeto criado recentemente, e crie e execute a função com o Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Se estiver a ocorrer esta exceção: `javax.xml.bind.JAXBException` com o Java 9, veja a solução no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Verá esta saída quando a função estiver a ser executada localmente no seu sistema e pronta para responder a pedidos HTTP:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Acione a função na linha de comandos com o curl numa janela de terminal nova:

```
curl -w "\n" http://localhost:7071/api/hello -d LocalFunction
```

```Output
Hello LocalFunction!
```

Utilize `Ctrl-C` no terminal para parar o código de função.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Inicie sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Implemente o código numa aplicação de funções nova com o destino do Maven `azure-functions:deploy`. Isso executa uma [Zip implementar com a execução do pacote](functions-deployment-technologies.md#zip-deploy) modo ativado.

> [!NOTE]
> Quando utiliza o Visual Studio Code para implementar a sua aplicação de função, lembre-se de escolher uma assinatura não gratuitos ou obterá um erro. Pode assistir a sua subscrição no lado esquerdo do IDE.

```
mvn azure-functions:deploy
```

Quando a implementação estiver concluída, verá o URL que pode utilizar para aceder à sua aplicação de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Teste a aplicação de funções em execução no Azure com `cURL`: Terá de alterar o URL do exemplo abaixo para corresponder ao URL implementado da sua própria aplicação de funções do passo anterior.

> [!NOTE]
> Certifique-se de definir o **direitos de acesso** para `Anonymous`. Ao escolher o nível padrão `Function`, tem de apresentar o [tecla de função](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) nos pedidos para o ponto final de função de acesso.

```
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e reimplementar

Edite o ficheiro de origem `src/main.../Function.java` no projeto gerado para alterar o texto devolvido pela sua aplicação de funções. Altere esta linha:

```java
return request.createResponse(200, "Hello, " + name);
```

Para a seguinte:

```java
return request.createResponse(200, "Hi, " + name);
```

Guarde as alterações. Pacote de limpa arquétipo execução e volte a implementar através da execução `azure-functions:deploy` a partir do terminal como antes. A aplicação de funções será atualizada e este pedido:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Terá resultados atualizados:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Passos Seguintes

Criou uma aplicação de funções do Java com um acionador HTTP simples e implementou o mesmo nas Funções do Azure.

- Reveja o [Guia para programadores das Funções do Java](functions-reference-java.md) para obter mais informações sobre a programação de funções do Java.
- Adicione mais funções com diferentes acionadores ao seu projeto com o destino do Maven `azure-functions:add`.
- Escreva e depure funções localmente com o [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md). 
- Depure funções implementadas no Azure com o Visual Studio Code. Veja a documentação do Visual Studio Code sobre [aplicações Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) para obter instruções.
