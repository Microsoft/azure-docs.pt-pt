---
title: Criar uma função Java a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função Java a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050431"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função Java em Azure a partir da linha de comando

> [!div class="op_single_selector" title1="Selecione o seu idioma de função: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Utilize ferramentas de linha de comando para criar uma função Java que responda a pedidos HTTP. Teste o código localmente e, em seguida, insiure-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre um pequeno custo de alguns USD centavo ou menos na sua <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">Conta do Azure</abbr>.

Se a Maven não é a sua ferramenta de desenvolvimento preferida, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)

+ A versão 2.4 ou mais tarde do [Azure CLI.](/cli/azure/install-azure-cli)

+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11. A `JAVA_HOME` variável ambiente deve ser definida para o local de instalação da versão correta do JDK.

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

### <a name="prerequisite-check"></a>Verificação pré-requisito

+ Em um terminal ou janela de comando, correr `func --version` para verificar que o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com funções Azure no seu computador local.">Azure Functions Core Tools</abbr> são a versão 3.x.

+ Corra `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ Corra `az login` para iniciar seduca no Azure e verifique uma subscrição ativa.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um específico <abbr title="O tipo de evento que invoca o código da função, como um pedido HTTP, uma mensagem de fila ou uma hora específica.">gatilho</abbr>. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

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

    <br/>
    <details>
    <summary><strong>Para executar funções em Java 11</strong></summary>

    Se quiser que as suas funções sejam executadas no Java 11, utilize `-DjavaVersion=11`. Para saber mais, consulte as [versões Java.](functions-reference-java.md#java-versions)
    </details>

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

<br/>
<details>
<summary><strong>O que é criado na pasta LocalFunctionProj?</strong></summary>

Esta pasta contém vários ficheiros para o projeto, tais como *Function.java*, *FunctionTest.java*, e *pom.xml*. Existem também ficheiros de configuraçõeslocal.settings.js[ ligados](functions-run-local.md#local-settings-file) e [host.jsem](functions-host-json.md). Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*
</details>

<br/>
<details>
<summary><strong>Código para função.java</strong></summary>

*Função.java* contém um `run` método que recebe dados de pedido na `request` variável é um [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) que está decorado com a anotação [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) que define o comportamento do gatilho. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A mensagem de resposta é gerada pela [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

O arquétipo também gera um teste de unidade para a sua função. Quando alterar a sua função para adicionar encadernações ou adicionar novas funções ao projeto, também terá de modificar os testes no ficheiro *FunctionTest.java.*
</details>

<br/>
<details>
<summary><strong>Código para pom.xml</strong></summary>

As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no elemento de **configuração** do plugin com um **grupoId** do `com.microsoft.azure` ficheiro *pom.xml* gerado. Por exemplo, o elemento de configuração abaixo instrui uma implementação baseada em Maven para criar uma aplicação de função no `java-functions-group` grupo de recursos no `westus` <abbr title="Uma referência geográfica a um centro de dados específico do Azure no qual os recursos são atribuídos.">region</abbr>. A própria aplicação de função funciona no Windows hospedado no `java-functions-app-service-plan` plano, que por padrão é um plano de consumo sem servidor.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Pode alterar estas definições para controlar a forma como os recursos são criados no Azure, tais como alterando `runtime.os` de antes da `windows` `linux` implantação inicial. Para obter uma lista completa de definições suportadas pelo plug-in Maven, consulte os detalhes da [configuração](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Executar a função localmente

1. **Executar a sua função** iniciando o anfitrião local do tempo de execução Azure Functions a partir da pasta *LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    No final da saída, devem aparecer as seguintes linhas:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Se httpExample não aparecer como mostrado acima, é provável que tenha iniciado o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize <kbd>ctrl+C</kbd> para parar o hospedeiro, navegue na pasta raiz do projeto e volte a executar o comando anterior.

1. **Copie o URL** da sua `HttpExample` função desta saída para um browser e apencha a cadeia de `?name=<YOUR_NAME>` consulta, fazendo com que o URL completo seja semelhante `http://localhost:7071/api/HttpExample?name=Functions` a . O navegador deve exibir uma mensagem `Hello Functions` como:

    ![Resultado da função executado localmente no navegador](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando terminar, use <kbd>Ctrl+C</kbd> e escolha <kbd>y</kbd> para parar o hospedeiro de funções.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Implementar o projeto de função para Azure

Uma aplicação de função e recursos relacionados são criados em Azure quando implementa o seu projeto de funções pela primeira vez. As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no ficheiro *pom.xml.* Neste artigo, você aceitará os incumprimentos.

<br/>
<details>
<summary><strong>Para criar uma aplicação de função em execução no Linux</strong></summary>

Para criar uma aplicação de função em execução no Linux em vez do Windows, altere `runtime.os` o elemento no ficheiro *pom.xml* `windows` de `linux` . A execução do Linux num plano de consumo é apoiada [nestas regiões.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Não é possível ter aplicações que são executadas no Linux e aplicações que funcionam no Windows no mesmo grupo de recursos.
</details>

1. Antes de iniciar a sua súm, inscreva-se na sua subscrição Azure usando a Azure CLI ou a Azure PowerShell. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [de login az](/cli/azure/reference-index#az-login) assina-o na sua conta Azure.

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

    A implementação embala os ficheiros do projeto e implanta-os na nova aplicação de função utilizando a [implementação zip](functions-deployment-technologies.md#zip-deploy). O código é executado a partir do pacote de implantação em Azure.

<br/>
<details>
<summary><strong>O que é criado em Azure?</strong></summary>

+ Grupo de recursos. Nomeado como _grupo java-funções._
+ Conta de armazenamento. Requerido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de hospedagem. Hospedagem sem servidor para a sua aplicação de função na região _de Westus._ O nome é _java-funções-app-service-plan_.
+ App de função. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é gerado aleatoriamente com base no seu _artefactoId,_ anexado com um número gerado aleatoriamente.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, **invoca-o fazendo um pedido HTTP ao seu URL** no navegador ou com uma ferramenta como <abbr title="Uma ferramenta de linha de comando para gerar pedidos HTTP a um URL; ver https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Browser](#tab/browser)

Copie o **URL completo do Invoke** mostrado na saída do comando numa barra de `publish` endereços do navegador, anexando o parâmetro de consulta `&name=Functions` . O navegador deve apresentar uma saída semelhante à de quando executou a função localmente.

![A saída da função executada no Azure num browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Corra [`curl`](https://curl.haxx.se/) com o URL **invocado,** anexando o parâmetro `&name=Functions` . A saída do comando deve ser o texto"Olá Funções".

![A saída da função executada em Azure usando o caracol](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Limpar recursos

Se continuar a seguir o [passo seguinte](#next-steps) e adicionar um Azure Storage <abbr title="No Azure Storage, um meio de associar uma função a uma fila de armazenamento, para que possa criar mensagens na fila.">encadernação de saída de fila</abbr>Mantenha todos os seus recursos no lugar, pois construirá o que já fez.

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

<br>
<hr/>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
