---
title: Criar uma função em Azure que responda aos pedidos do HTTP
description: Aprenda a criar uma função a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor estiva em Funções Azure.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c1e1b9912109ae8a7361c9d0d776ca1810a90d5c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886641"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Quickstart: Criar uma função em Azure que responda aos pedidos do HTTP

Neste artigo, utiliza ferramentas de linha de comando para criar uma função que responda aos pedidos do HTTP. Depois de testar o código localmente, implementa-o para o ambiente sem servidor estoirar das Funções Azure. Completar este quickstart incorre num pequeno custo de alguns cêntimos de USD ou menos na sua conta Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Há também uma [versão baseada em Código](functions-create-first-function-vs-code.md) de Estúdio Visual deste artigo.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Se a Maven não é a sua ferramenta de desenvolvimento preferencial, confira os nossos tutoriais semelhantes para os devlopers java usando [Gradle,](/azure/azure-functions/functions-create-first-java-gradle) [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) e [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Nas Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Executar `func init` o comando, da seguinte forma, para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
---

Maven pede-lhe valores necessários para terminar gerando o projeto na implantação.   
Forneça os seguintes valores quando solicitado:

| Mensagem | Valor | Descrição |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as regras de [nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) do pacote para a Java. |
| **artefactoId** | `fabrikam-functions` | Um valor que é o nome do jarro, sem um número de versão. |
| **Versão** | `1.0-SNAPSHOT` | Escolha o valor padrão. |
| **pacote** | `com.fabrikam.functions` | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. |

Digite `Y` ou prima Introduza para confirmar.

Maven cria os ficheiros do projeto numa nova pasta com `fabrikam-functions`um nome de _artefactoId_, que neste exemplo é . 
::: zone-end  
Navegue na pasta do projeto:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *as configurações locais.json* podem conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do ficheiro

Se desejar, pode saltar para [executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contém `Run` um método que recebe `req` dados de pedido na variável é um [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) que é decorado com o **HttpTriggerAttribute**, que define o comportamento do gatilho. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que devolve uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Função.java
*Function.java* contém `run` um método que recebe `request` dados de pedido na variável é um [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) que é decorado com a anotação [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) que define o comportamento do gatilho. 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

A mensagem de resposta é gerada pela [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

#### <a name="pomxml"></a>pom.xml

As definições para os recursos Azure criados para hospedar a sua `com.microsoft.azure` app são definidas no elemento de **configuração** do plugin com um **groupId** no ficheiro pom.xml gerado. Por exemplo, o elemento de configuração abaixo instrui uma implementação `java-functions-group` baseada em `westus` Maven para criar uma aplicação de função no grupo de recursos na região. A aplicação de funções em `java-functions-app-service-plan` si funciona no Windows hospedado no plano, que por padrão é um plano de consumo sem servidores.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

Pode alterar estas definições para controlar a forma como `runtime.os` os `windows` `linux` recursos são criados no Azure, como por exemplo, alterando-se de para antes da implementação inicial. Para obter uma lista completa de definições suportadas pelo plug-in Maven, consulte os detalhes da [configuração](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

O arquétipo também gera um teste de unidade para a sua função. Quando alterar a sua função para adicionar encadernações ou adicionar novas funções ao projeto, também terá de modificar os testes no ficheiro *FunctionTest.java.*
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* `main()` contém uma função Python que é desencadeada de acordo com a configuração em *função.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Para um gatilho HTTP, a função `req` recebe dados de pedido na variável tal como definido na *função.json*. `req`é um exemplo da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de `$return` retorno, definido como em *função.json,* é um exemplo de [azure.functions.functions.httpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporta uma função que é desencadeada de acordo com a configuração em *função.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Para um gatilho HTTP, a função `req` recebe dados de pedido na variável tal como definido na *função.json*. O objeto de `$return` retorno, definido como em *função.json,* é a resposta. Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é desencadeada de acordo com a configuração em *função.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função `req` recebe dados de pedido na variável do tipo **HttpRequest** conforme definido na *função.json*. O objeto de `$return` retorno, definido como em *função.json,* é a resposta. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* define um script de função que é desencadeado de acordo com a configuração em *função.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função `$Request` recebe dados de pedido passados para o param definido em *função.json*. O objeto de `Response` retorno, definido como em *função.json,* é passado para o `Push-OutputBinding` cmdlet como resposta. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* é um ficheiro de configuração `bindings` que define a entrada e a saída para a função, incluindo o tipo de gatilho. 
::: zone-end

::: zone pivot="programming-language-python"
Pode mudar `scriptFile` para invocar um ficheiro Python diferente, se desejar.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma [`httpTrigger`](functions-bindings-http-webhook-trigger.md) ligação de [`http`](functions-bindings-http-webhook-output.md)entrada de tipo e de ligação de saída do tipo .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de apoio para a sua função

Antes de poder implementar o seu código de função para o Azure, precisa de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de função, que fornece o ambiente para executar o seu código de função. Uma aplicação de função mapeia o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Utilize os seguintes comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

Se ainda não o fez, inicie sessão no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria `AzureFunctionsQuickstart-rg` um `westeurope` grupo de recursos nomeado na região. (Geralmente cria o seu grupo de recursos e recursos numa `az account list-locations` região próxima de si, utilizando uma região disponível a partir do comando.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Não é possível hospedar aplicações linux e Windows no mesmo grupo de recursos. Se tiver um grupo de `AzureFunctionsQuickstart-rg` recursos existente nomeado com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.
 
    
Crie uma conta de armazenamento geral no seu grupo de recursos e região utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, `<STORAGE_NAME>` substitua-o por um nome globalmente único adequado a si. Os nomes devem conter três a 24 caracteres e apenas letras minúsculas. `Standard_LRS`especifica uma conta de fim geral, que é [suportada por Funções.](storage-considerations.md#storage-account-requirements)

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

A conta de armazenamento incorre apenas alguns cêntimos (USD) para este arranque rápido.
    
Criar a aplicação de função utilizando o [app de funções az criar](/cli/azure/functionapp#az-functionapp-create) comando. No exemplo seguinte, `<STORAGE_NAME>` substitua-o pelo nome da conta que `<APP_NAME>` usou no passo anterior e substitua-o por um nome globalmente único adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
::: zone-end  

::: zone pivot="programming-language-python"  
Se estiver a utilizar python `--runtime-version` 3.8, mude para `3.8` e `--functions_version` para `3`.

Se estiver a utilizar python `--runtime-version` 3.6, mude para `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Se estiver a usar o Nó.js 8, mude `--runtime-version` também para `8`.


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado no âmbito do Plano de Consumo de [Funções Azure,](functions-scale.md#consumption-plan)que é gratuito pela quantidade de utilização que incorra aqui. O comando também prevê uma instância de Insights de Aplicação Azure associada no mesmo grupo de recursos, com o qual pode monitorizar a sua aplicação de função e visualizar registos. Para mais informações, consulte as [Funções Monitor Azure](functions-monitoring.md). A instância não incorre em custos até que o ative.
    
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para o Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Antes de utilizar as Ferramentas Core para implementar o seu projeto para o Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

O seguinte comando prepara o seu projeto TypeScript para implementação:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Com os recursos necessários, está agora pronto para implementar o seu projeto de funções locais para a aplicação de funções em Azure, utilizando o comando [de publicação func azure functionapp.](functions-run-local.md#project-file-deployment) No exemplo seguinte, `<APP_NAME>` substitua-o pelo nome da sua aplicação.

```
func azure functionapp publish <APP_NAME>
```

Se vir o erro, "Não consigo encontrar a app com nome...", espere alguns segundos e tente `az functionapp create` novamente, já que o Azure pode não ter inicializado totalmente a aplicação após o comando anterior.

O comando de publicação apresenta resultados semelhantes aos seguintes resultados (truncados para a simplicidade):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para o Azure

Uma aplicação de função e recursos relacionados são criados no Azure quando você implementa o seu projeto de funções pela primeira vez. As definições para os recursos Azure criados para hospedar a sua app são definidas no [ficheiro pom.xml](#pomxml). Neste artigo, aceitará os incumprimentos.

> [!TIP]
> Para criar uma aplicação de função em `runtime.os` linha em linux em `windows` `linux`vez do Windows, altere o elemento no ficheiro pom.xml de . A execução do Linux num plano de consumo é apoiada [nestas regiões.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Não pode ter aplicações que funcionam no Linux e aplicações que funcionam no Windows no mesmo grupo de recursos.

Antes de ser implementado, utilize o comando [az login](/cli/azure/authenticate-azure-cli) Azure CLI para iniciar sessão na sua subscrição Azure. 

```azurecli
az login
```

Utilize o seguinte comando para implementar o seu projeto numa nova aplicação de funções. 

```
mvn azure-functions:deploy
```

Isto cria os seguintes recursos em Azure:

+ Grupo de recursos. Nomeado como _java-functions-group_.
+ Conta de armazenamento. Exigido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de hospedagem. Hospedagem sem servidor estoque para a sua aplicação de funções na região _de Westus._ O nome é _java-functions-app-service-plan_.
+ App de funções. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é gerado aleatoriamente com base no seu _artefactoId,_ anexado com um número gerado aleatoriamente. 

A implementação embala os ficheiros do projeto e implanta-os na nova aplicação de funções utilizando [a implementação do zip](functions-deployment-technologies.md#zip-deploy). O código é executado a partir do pacote de implantação em Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido http para o seu URL no navegador ou com uma ferramenta como o curl. Em ambos os `code` casos, o parâmetro URL é a sua [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) única que autoriza a invocação do ponto final da sua função.

# <a name="browser"></a>[Browser](#tab/browser)

Copie o **URL invocado** completo mostrado na saída do comando de publicação numa `&name=Functions`barra de endereços de navegador, anexando o parâmetro de consulta . O navegador deve apresentar uma saída semelhante à que executou a função localmente.

![A saída da função é executada no Azure num browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[caracol](#tab/curl)

Corra [`curl`](https://curl.haxx.se/) com o **URL invocado,** afunilhe o parâmetro `&name=Functions`. A saída do comando deve ser o texto, "Olá Funções".

![A saída da função funciona em Azure usando caracóis](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Para visualizar registos em tempo real para uma aplicação de função publicada, utilize o [Fluxo de Métricas ao Vivo](functions-monitoring.md#streaming-logs)insights da aplicação Insights .

## <a name="clean-up-resources"></a>Limpar recursos

Se continuar até ao próximo passo, Adicione uma ligação de saída de fila de [armazenamento Azure,](functions-add-output-binding-storage-queue-cli.md)mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento azure](functions-add-output-binding-storage-queue-cli.md)
