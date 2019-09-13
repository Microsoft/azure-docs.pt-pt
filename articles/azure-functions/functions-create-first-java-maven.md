---
title: Usar Java e Maven para publicar uma função-Azure Functions
description: Crie e publique uma função acionada por HTTP simples no Azure com o Java e o Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.topic: quickstart
ms.devlang: java
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 381298abcfa4fa707af6b382565b4bfb990e96e6
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899506"
---
# <a name="quickstart-use-java-to-create-and-publish-a-function-to-azure-functions"></a>Início rápido: Use Java para criar e publicar uma função para Azure Functions

Este artigo orienta como criar e publicar uma função Java no Azure Functions com a ferramenta de linha de comando do Maven. Quando você terminar, o código de função será executado no [plano de consumo](functions-scale.md#consumption-plan) no Azure e poderá ser disparado usando uma solicitação HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3,0 ou superior
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior

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
> Se você estiver tendo problemas com a execução do comando, dê uma olhada em `maven-archetype-plugin` qual versão é usada. Como você está executando o comando em um diretório vazio sem nenhum `.pom` arquivo, ele pode estar tentando usar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` se você tiver atualizado seu Maven de uma versão mais antiga. Nesse caso, tente excluir o `maven-archetype-plugin` diretório e executar novamente o comando.

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
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

O Maven cria os ficheiros de projeto numa pasta nova, com o nome _artifactId_, `fabrikam-functions` neste exemplo. O código pronto para executar gerado no projeto é uma função disparada por [http](/azure/azure-functions/functions-bindings-http-webhook) que retorna o corpo da solicitação. Substitua *src/main/java/com/fabrikam/Functions/function. java* pelo seguinte código: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java
     * 2. curl {your host}/api/HttpTrigger-Java?name=HTTP%20Query
     */
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
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

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Executar localmente a função

Altere o diretório para a pasta de projeto recém-criada (aquela que contém os arquivos host. JSON e pom. xml) e compile e execute a função com o Maven:

```CMD
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

   hello: http://localhost:7071/api/HttpTrigger-Java
```

Acione a função na linha de comandos com o curl numa janela de terminal nova:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d LocalFunction
```

```Output
Hello LocalFunction!
```

Utilize `Ctrl-C` no terminal para parar o código de função.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementação para as Funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Entre com o CLI do Azure antes de](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) continuar.

```azurecli
az login
```

Implemente o código numa aplicação de funções nova com o destino do Maven `azure-functions:deploy`. Isso executa uma [implantação de zip com a execução do modo de pacote](functions-deployment-technologies.md#zip-deploy) habilitada.

> [!NOTE]
> Quando você usar Visual Studio Code para implantar seu aplicativo de funções, lembre-se de escolher uma assinatura não livre ou receberá um erro. Você pode assistir à sua assinatura no lado esquerdo do IDE.

```azurecli
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
> Certifique-se de definir os direitos de `Anonymous` **acesso** como. Quando você escolhe o nível padrão de `Function`, é necessário apresentar a chave de [função](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) em solicitações para acessar seu ponto de extremidade de função.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e reimplementar

Edite o ficheiro de origem `src/main.../Function.java` no projeto gerado para alterar o texto devolvido pela sua aplicação de funções. Altere esta linha:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
```

Para a seguinte:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hi, " + name).build();
```

Guarde as alterações. Execute o pacote limpo do MVN e reimplante `azure-functions:deploy` -o executando a partir do terminal como antes. A aplicação de funções será atualizada e este pedido:

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
