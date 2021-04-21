---
title: Criar uma função Java a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função Java a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 1a1571e83e33ee0aec40df4cf68b3d01b6ef5f04
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787508"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função Java em Azure a partir da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, utiliza ferramentas de linha de comando para criar uma função Java que responda a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

> [!NOTE]
> Se a Maven não é a sua ferramenta de desenvolvimento preferida, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)

+ A versão 2.4 ou mais tarde do [Azure CLI.](/cli/azure/install-azure-cli)

+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11. A `JAVA_HOME` variável ambiente deve ser definida para o local de instalação da versão correta do JDK.     

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

### <a name="prerequisite-check"></a>Verificação pré-requisito

+ Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 3.x.

+ Corra `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ Corra `az login` para iniciar seduca no Azure e verifique uma subscrição ativa.

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

1. Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Se quiser que as suas funções sejam executadas no Java 11, utilize `-DjavaVersion=11`. Para saber mais, consulte as [versões Java.](functions-reference-java.md#java-versions) 
    > + A `JAVA_HOME` variável ambiente deve ser definida para a localização de instalação da versão correta do JDK para completar este artigo.

1. A Maven pede-lhe valores necessários para terminar a geração do projeto na implantação.   
    Fornecer os seguintes valores quando solicitado:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as [regras de nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) de pacotes para a Java. |
    | **artefactoId** | `fabrikam-functions` | Um valor que é o nome do jarro, sem um número de versão. |
    | **Versão** | `1.0-SNAPSHOT` | Escolha o valor predefinido. |
    | **pacote** | `com.fabrikam` | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. |

1. Digite `Y` ou prima Enter para confirmar.

    A Maven cria os ficheiros do projeto numa nova pasta com o nome de _artifactId_, que neste exemplo é `fabrikam-functions` . 

1. Navegue na pasta do projeto:

    ```console
    cd fabrikam-functions
    ```

    Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsligados .](functions-host-json.md) Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examine o conteúdo do ficheiro

Se desejar, pode saltar para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

#### <a name="functionjava"></a>Função.java
*Função.java* contém um `run` método que recebe dados de pedido na `request` variável é um [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) que está decorado com a anotação [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) que define o comportamento do gatilho. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A mensagem de resposta é gerada pela [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

#### <a name="pomxml"></a>pom.xml

As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no elemento de **configuração** do plugin com um **grupoId** do `com.microsoft.azure` ficheiro pom.xml gerado. Por exemplo, o elemento de configuração abaixo instrui uma implementação baseada em Maven para criar uma aplicação de função no `java-functions-group` grupo de recursos na `westus` região. A própria aplicação de função funciona no Windows hospedado no `java-functions-app-service-plan` plano, que por padrão é um plano de consumo sem servidor.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Pode alterar estas definições para controlar a forma como os recursos são criados no Azure, tais como alterando `runtime.os` de antes da `windows` `linux` implantação inicial. Para obter uma lista completa de definições suportadas pelo plug-in Maven, consulte os detalhes da [configuração](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunçãoTest.java

O arquétipo também gera um teste de unidade para a sua função. Quando alterar a sua função para adicionar encadernações ou adicionar novas funções ao projeto, também terá de modificar os testes no ficheiro *FunctionTest.java.*

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Executar a sua função iniciando o anfitrião local do tempo de execução Azure Functions a partir da pasta *LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    No final da saída, devem aparecer as seguintes linhas:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Se httpExample não aparecer como mostrado acima, é provável que tenha iniciado o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize **o Ctrl** + **C** para parar o hospedeiro, navegue na pasta raiz do projeto e volte a executar o comando anterior.

1. Copie o URL da sua `HttpExample` função desta saída para um browser e apencha a cadeia de `?name=<YOUR_NAME>` consulta, fazendo com que o URL completo seja semelhante `http://localhost:7071/api/HttpExample?name=Functions` a . O navegador deve exibir uma mensagem que ecoe o valor da sua cadeia de consulta. O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando terminar, use **ctrl** + **C** e opte por `y` parar o hospedeiro de funções.

## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para Azure

Uma aplicação de função e recursos relacionados são criados em Azure quando implementa o seu projeto de funções pela primeira vez. As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no [ficheiropom.xml.](#pomxml) Neste artigo, você aceitará os incumprimentos.

> [!TIP]
> Para criar uma aplicação de função em execução no Linux em vez do Windows, altere `runtime.os` o elemento no ficheiro pom.xml de `windows` `linux` . A execução do Linux num plano de consumo é apoiada [nestas regiões.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Não é possível ter aplicações que são executadas no Linux e aplicações que funcionam no Windows no mesmo grupo de recursos.

1. Antes de iniciar a sua súm, inscreva-se na sua subscrição Azure usando a Azure CLI ou a Azure PowerShell. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [de login az](/cli/azure/reference-index#az_login) assina-o na sua conta Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    O [cmdlet Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) indica-o na sua conta Azure.

    ---

1. Utilize o seguinte comando para implantar o seu projeto numa nova aplicação de função.

    ```console
    mvn azure-functions:deploy
    ```
    
    Isto cria os seguintes recursos em Azure:
    
    + Grupo de recursos. Nomeado como _grupo java-funções._
    + Conta de armazenamento. Requerido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
    + Plano de hospedagem. Hospedagem sem servidor para a sua aplicação de função na região _de Westus._ O nome é _java-funções-app-service-plan_.
    + App de função. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é gerado aleatoriamente com base no seu _artefactoId,_ anexado com um número gerado aleatoriamente.
    
    A implementação embala os ficheiros do projeto e implanta-os na nova aplicação de função utilizando a [implementação zip](functions-deployment-technologies.md#zip-deploy). O código é executado a partir do pacote de implantação em Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se continuar até ao [próximo passo](#next-steps) e adicionar uma bagagem de saída da fila Azure Storage, mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

 # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
