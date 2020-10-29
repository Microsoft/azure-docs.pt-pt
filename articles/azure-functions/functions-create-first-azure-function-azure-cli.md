---
title: Criar uma função no Azure que responda a pedidos HTTP
description: Aprenda a criar uma função a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b7b46f2d280577f40f927a0d8eb6fcf2ed33e04a
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927435"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Quickstart: Criar uma função no Azure que responda aos pedidos HTTP

::: zone pivot="programming-language-csharp"  
Neste artigo, utiliza ferramentas de linha de comando para criar uma função baseada na biblioteca de classe C# que responda a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end  
::: zone pivot="programming-language-javascript"
Neste artigo, utiliza ferramentas de linha de comando para criar uma função JavaScript que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end
::: zone pivot="programming-language-typescript"
Neste artigo, utiliza ferramentas de linha de comando para criar uma função TypeScript que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end   
::: zone pivot="programming-language-powershell"
Neste artigo, utiliza ferramentas de linha de comando para criar uma função PowerShell que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end  
::: zone pivot="programming-language-python" 
Neste artigo, utiliza ferramentas de linha de comando para criar uma função Python que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end  
::: zone pivot="programming-language-java" 
Neste artigo, utiliza ferramentas de linha de comando para criar uma função Java que responda a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure. 
::: zone-end

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Há também uma [versão baseada em Código visual](functions-create-first-function-vs-code.md) do Estúdio deste artigo.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Se a Maven não é a sua ferramenta de desenvolvimento preferencial, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Executar o `func init` comando, da seguinte forma, para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  
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

> [!IMPORTANT]
> + Utilize `-DjavaVersion=11` se quiser que as suas funções funcionem em Java 11. Para saber mais, consulte as [versões Java.](functions-reference-java.md#java-versions) 
> + A `JAVA_HOME` variável ambiente deve ser definida para a localização de instalação da versão correta do JDK para completar este artigo.

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

A Maven pede-lhe valores necessários para terminar a geração do projeto na implantação.   
Fornecer os seguintes valores quando solicitado:

| Prompt | Valor | Descrição |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as [regras de nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) de pacotes para a Java. |
| **artefactoId** | `fabrikam-functions` | Um valor que é o nome do jarro, sem um número de versão. |
| **Versão** | `1.0-SNAPSHOT` | Escolha o valor predefinido. |
| **pacote** | `com.fabrikam` | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. |

Digite `Y` ou prima Enter para confirmar.

A Maven cria os ficheiros do projeto numa nova pasta com o nome de _artifactId_ , que neste exemplo é `fabrikam-functions` . 

::: zone-end  
Navegue na pasta do projeto:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsligados .](functions-host-json.md) Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examine o conteúdo do ficheiro

Se desejar, pode saltar para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contém um `Run` método que recebe dados de pedido na `req` variável é um [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) que está decorado com o **HttpTriggerAttribute** , que define o comportamento do gatilho. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que devolve uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Função.java
*Fun.java* contém um `run` método que recebe dados de pedido na `request` variável é um [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) que está decorado com a anotação [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) que define o comportamento do gatilho. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A mensagem de resposta é gerada pela [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

#### <a name="pomxml"></a>pom.xml

As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no elemento de **configuração** do plugin com um **grupoId** do `com.microsoft.azure` ficheiro pom.xml gerado. Por exemplo, o elemento de configuração abaixo instrui uma implementação baseada em Maven para criar uma aplicação de função no `java-functions-group` grupo de recursos na `westus` região. A própria aplicação de função funciona no Windows hospedado no `java-functions-app-service-plan` plano, que por padrão é um plano de consumo sem servidor.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Pode alterar estas definições para controlar a forma como os recursos são criados no Azure, tais como alterando `runtime.os` de antes da `windows` `linux` implantação inicial. Para obter uma lista completa de definições suportadas pelo plug-in Maven, consulte os detalhes da [configuração](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunçãoTest.java

O arquétipo também gera um teste de unidade para a sua função. Quando alterar a sua função para adicionar encadernações ou adicionar novas funções ao projeto, também terá de modificar os testes no ficheiro *FunctionTest.java.*
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init \_ \_ .py

*\_ \_ init \_ \_ .py* contém uma `main()` função Python que é desencadeada de acordo com a configuração em *function.jsem* .

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` tal como definida emfunction.js *em* . `req` é um exemplo da [classe azure.funções.HttpRequest class](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.js,* é um exemplo de [azure.functions.httpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporta uma função que é desencadeada de acordo com a configuração em *function.jsem* .

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` tal como definida emfunction.js *em* . O objeto de retorno, definido como `$return` em *function.js,* é a resposta. Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é desencadeada de acordo com a configuração em *function.jsem* .

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` do tipo **HttpRequest,** tal como definido em *function.jsem* . O objeto de retorno, definido como `$return` em *function.js,* é a resposta. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* define um script de função que é desencadeado de acordo com a configuração em *function.jsem* .

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função recebe dados de pedido passados para o `$Request` param definido em *function.jsem* . O objeto de retorno, definido como `Response` em *function.js,* é passado para o `Push-OutputBinding` cmdlet como resposta. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.jsligado* é um ficheiro de configuração que define a entrada e saída `bindings` para a função, incluindo o tipo de gatilho. 
::: zone-end

::: zone pivot="programming-language-python"
Pode alterar `scriptFile` para invocar um ficheiro Python diferente, se desejar.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e da ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md) .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de suporte para a sua função

Antes de poder implantar o seu código de função para Azure, tem de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função mapeia para o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão, implementação e partilha mais fácil de recursos.

Utilize os seguintes comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

Se ainda não o fez, inscreva-se no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```
    
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos nomeado `AzureFunctionsQuickstart-rg` na `westeurope` região. (Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível a partir do `az account list-locations` comando.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Não é possível hospedar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente nomeado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.
 
    
Crie uma conta de armazenamento para fins gerais no seu grupo de recursos e região utilizando a [conta de armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, `<STORAGE_NAME>` substitua-o por um nome globalmente único, adequado a si. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

A conta de armazenamento incorre apenas alguns cêntimos (USD) para este arranque rápido.
    
Crie a aplicação de função utilizando o comando [az functionapp.](/cli/azure/functionapp#az-functionapp-create) No exemplo seguinte, `<STORAGE_NAME>` substitua-o pelo nome da conta que usou no passo anterior e substitua-o `<APP_NAME>` por um nome globalmente único, adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
::: zone-end  

::: zone pivot="programming-language-python"  
Se estiver a utilizar o Python 3.8, mude `--runtime-version` de `3.8` e para `--functions_version` `3` .

Se estiver a utilizar o Python 3.6, mude `--runtime-version` para `3.6` .

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Se estiver a utilizar Node.js 8, também `--runtime-version` mude para `8` .


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
Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado ao abrigo do Plano de [Consumo de Funções Azure](functions-scale.md#consumption-plan), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.
    
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Antes de utilizar as Ferramentas Core para implantar o seu projeto no Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

O seguinte comando prepara o seu projeto TypeScript para implantação:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Com os recursos necessários, está agora pronto para implementar o seu projeto de funções locais para a aplicação de funções em Azure, utilizando o comando [de publicação do func azure functionapp.](functions-run-local.md#project-file-deployment) No exemplo seguinte, `<APP_NAME>` substitua-a pelo nome da sua aplicação.

```
func azure functionapp publish <APP_NAME>
```

Se vir o erro, "Não consigo encontrar a app com o nome...", aguarde alguns segundos e tente novamente, uma vez que o Azure pode não ter inicializado totalmente a app após o `az functionapp create` comando anterior.

O comando de publicação apresenta resultados semelhantes à seguinte saída (truncada para a simplicidade):

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
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para Azure

Uma aplicação de função e recursos relacionados são criados em Azure quando implementa o seu projeto de funções pela primeira vez. As definições para os recursos Azure criados para hospedar a sua aplicação são definidas no [ficheiropom.xml.](#pomxml) Neste artigo, você aceitará os incumprimentos.

> [!TIP]
> Para criar uma aplicação de função em execução no Linux em vez do Windows, altere `runtime.os` o elemento no ficheiro pom.xml de `windows` `linux` . A execução do Linux num plano de consumo é apoiada [nestas regiões.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Não é possível ter aplicações que são executadas no Linux e aplicações que funcionam no Windows no mesmo grupo de recursos.

Antes de poder implementar, utilize o comando [Azure](/cli/azure/authenticate-azure-cli) CLI do login para iniciar sessão na sua subscrição Azure. 

```azurecli
az login
```

Utilize o seguinte comando para implantar o seu projeto numa nova aplicação de função. 

```
mvn azure-functions:deploy
```

Isto cria os seguintes recursos em Azure:

+ Grupo de recursos. Nomeado como _grupo java-funções._
+ Conta de armazenamento. Requerido por Funções. O nome é gerado aleatoriamente com base nos requisitos de nome da conta de armazenamento.
+ Plano de hospedagem. Hospedagem sem servidor para a sua aplicação de função na região _de Westus._ O nome é _java-funções-app-service-plan_ .
+ App de função. Uma aplicação de função é a unidade de implementação e execução para as suas funções. O nome é gerado aleatoriamente com base no seu _artefactoId,_ anexado com um número gerado aleatoriamente. 

A implementação embala os ficheiros do projeto e implanta-os na nova aplicação de função utilizando a [implementação zip](functions-deployment-technologies.md#zip-deploy). O código é executado a partir do pacote de implantação em Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido HTTP ao seu URL no navegador ou com uma ferramenta como o curl. Em ambos os casos, o `code` parâmetro URL é a sua chave de [função](functions-bindings-http-webhook-trigger.md#authorization-keys) única que autoriza a invocação do ponto final da sua função.

# <a name="browser"></a>[Browser](#tab/browser)

Copie o **URL completo de invocação** mostrado na saída do comando de publicação numa barra de endereços do navegador, anexando o parâmetro de consulta `&name=Functions` . O navegador deve apresentar uma saída semelhante à de quando executou a função localmente.

![A saída da função executada no Azure num browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[caracóis](#tab/curl)

Corra [`curl`](https://curl.haxx.se/) com o URL **invocado,** anexando o parâmetro `&name=Functions` . A saída do comando deve ser o texto"Olá Funções".

![A saída da função executada em Azure usando o caracol](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Para visualizar registos em tempo real para uma aplicação de função publicada, utilize o [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).
>
> Executar o seguinte comando para abrir o fluxo de métricas ao vivo em um navegador.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Limpar recursos

Se continuar até ao próximo passo, [adicione uma ligação de saída de fila Azure Storage](functions-add-output-binding-storage-queue-cli.md), mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Para sair do ambiente virtual, `deactivate` corra.
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md)
 
