---
title: Crie a sua primeira função em Azure com Kotlin e Maven
description: Crie e publique uma função desencadeada http para Azure com Kotlin e Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 526287feb93052d10428971dfd8f660d4265c831
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89071266"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Quickstart: Crie a sua primeira função com Kotlin e Maven

Este artigo guia-o através da utilização da ferramenta de linha de comando Maven para construir e publicar um projeto de função Kotlin para as Funções Azure. Quando terminar, o seu código de função é executado no [Plano de Consumo](functions-scale.md#consumption-plan) no Azure e pode ser acionado com um pedido HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções utilizando o Kotlin, tem de ter as seguintes instalações:

- [Java Development Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [CLI do Azure](/cli/azure)
- [Azure Funções Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto das Funções

Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[festa](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Se está a ter problemas com a execução do comando, veja qual `maven-archetype-plugin` a versão utilizada. Como está a executar o comando num diretório vazio sem `.pom` ficheiro, pode estar a tentar usar um plugin da versão mais antiga `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` se atualizar o seu Maven a partir de uma versão mais antiga. Em caso afirmativo, tente apagar o `maven-archetype-plugin` diretório e re-executar o comando.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

A Maven pede-lhe valores necessários para terminar a geração do projeto. Para os valores de _groupId_, _artifactId_ e _versão_, veja a referência [Maven naming conventions (Convenções de nomenclatura do Maven)](https://maven.apache.org/guides/mini/guide-naming-conventions.html). O valor de _appName_ tem de ser exclusivo em todo o Azure, para que o Maven gere um nome de aplicação com base no _artifactId_ anteriormente introduzido por predefinição. O _valor do nome de pacote_ determina o pacote Kotlin para o código de função gerado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são utilizados como exemplo e para facilitar a leitura dos passos posteriores deste início rápido. És encorajado a fornecer os teus próprios valores à Maven neste passo.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

O Maven cria os ficheiros de projeto numa pasta nova, com o nome _artifactId_, `fabrikam-functions` neste exemplo. O código pronto a ser executado no projeto é uma função [acionada por HTTP](./functions-bindings-http-webhook.md) simples que ecoa o corpo do pedido:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto criado recentemente, e crie e execute a função com o Maven:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Se estiver a ocorrer esta exceção: `javax.xml.bind.JAXBException` com o Java 9, veja a solução no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Verá esta saída quando a função estiver a ser executada localmente no seu sistema e pronta para responder a pedidos HTTP:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Acione a função na linha de comandos com o curl numa janela de terminal nova:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Utilize `Ctrl-C` no terminal para parar o código de função.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Inscreva-se com o CLI Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Implemente o código numa aplicação de funções nova com o destino do Maven `azure-functions:deploy`.

> [!NOTE]
> Quando utilizar o Código do Estúdio Visual para implementar a sua aplicação Função, lembre-se de escolher uma subscrição não gratuita, ou terá um erro. Pode ver a sua subscrição no lado esquerdo do IDE.

```
mvn azure-functions:deploy
```

Quando a implementação estiver concluída, verá o URL que pode utilizar para aceder à sua aplicação de funções do Azure:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Teste a aplicação de funções em execução no Azure com `cURL`: Terá de alterar o URL do exemplo abaixo para corresponder ao URL implementado da sua própria aplicação de funções do passo anterior.

> [!NOTE]
> Certifique-se de que define os **direitos de acesso** a `Anonymous` . Ao escolher o nível predefinido de `Function` , é-lhe exigido que apresente a [tecla de função](functions-bindings-http-webhook-trigger.md#authorization-keys) nos pedidos de acesso ao ponto final da sua função.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e reimplementar

Edite o ficheiro de origem `src/main.../Function.java` no projeto gerado para alterar o texto devolvido pela sua aplicação de funções. Altere esta linha:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Para o seguinte código:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Guarde as alterações e implemente novamente ao executar `azure-functions:deploy` a partir do terminal, como fez anteriormente. A aplicação de funções será atualizada e este pedido:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Vê a saída atualizada:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Encadernações de referência

Para trabalhar com [funções acionamentos e ligações](functions-triggers-bindings.md) que não sejam o gatilho HTTP e o gatilho do temporizador, é necessário instalar extensões de ligação. Apesar de não ser exigido por este artigo, terá de saber como fazer extensões ao trabalhar com outros tipos de ligação.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função Kotlin com um simples gatilho HTTP e implantou-a para as Funções Azure.

- Reveja o  [guia de desenvolvimento de Funções de Java](functions-reference-java.md) para obter mais informações sobre o desenvolvimento das funções de Java e Kotlin.
- Adicione mais funções com diferentes acionadores ao seu projeto com o destino do Maven `azure-functions:add`.
- Escreva e depure funções localmente com o [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md). 
- Depure funções implementadas no Azure com o Visual Studio Code. Veja a documentação do Visual Studio Code sobre [aplicações Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) para obter instruções.
