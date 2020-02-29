---
title: Criar uma função em Azure que responda aos pedidos do HTTP
description: Aprenda a criar uma função a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor estiva em Funções Azure.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c8648a0bdfaeeb8a89ea24b0f49610e5bf5c0491
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190877"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Quickstart: Criar uma função em Azure que responda aos pedidos do HTTP

Neste artigo, utiliza ferramentas de linha de comando para criar uma função que responda aos pedidos do HTTP. Depois de testar o código localmente, implementa-o para o ambiente sem servidor estoirar das Funções Azure. Completar este quickstart incorre num pequeno custo de alguns cêntimos de USD ou menos na sua conta Azure.

Há também uma [versão baseada em Código](functions-create-first-function-vs-code.md) de Estúdio Visual deste artigo.

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ O [Azure Funs Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou uma versão posterior de 2.x.

+ A versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 ou posterior. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js,](https://nodejs.org/)itldes ative LTS e manutenção LTS (8.11.1 e 10.14.1 recomendados).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) ou [Python 3.6,](https://www.python.org/downloads/release/python-368/)que são apoiados por Funções Azure. As versões Python 3.8 e posteriores ainda não são suportadas. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Núcleo PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [Núcleo .NET SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Verifique o seu ambiente

+ Numa janela de terminal ou comando, faça `func --version` para verificar se as Ferramentas Core funções do Azure são a versão 2.7.1846 ou uma versão 2.x posterior.

+ Execute `az --version` para verificar se a versão Azure CLI é de 2.0.76 ou mais tarde.

+ Execute `az login` para iniciar sessão no Azure e verificar uma subscrição ativa.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Faça `node --version` para verificar se a sua versão Node.js reporta 8.x ou 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Execute `python --version` (Linux/MacOS) ou `py --version` (Windows) para verificar os relatórios da versão Python 3.7.x ou 3.6.x.

## <a name="create-venv"></a>Criar e ativar um ambiente virtual

Numa pasta adequada, execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`. Certifique-se de que utiliza Python 3.7 ou 3.6, que são suportados pelas Funções Azure.


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se python não instalou o pacote de veado na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Executa todos os comandos subsequentes neste ambiente virtual ativado. (Para sair do ambiente virtual, corra `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Nas Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

1. No ambiente virtual, execute o comando `func init` para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:

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


    Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *as configurações locais.json* podem conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

1. Navegue na pasta do projeto:

    ```
    cd LocalFunctionProj
    ```
    
1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o argumento `--name` é o nome único da sua função e o `--template` argumento especifica o gatilho da função. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` cria um ficheiro de código HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` cria uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração chamado *função.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do ficheiro

Se desejar, pode saltar para [executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contém um método `Run` que recebe dados de pedidos na variável `req` é um [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) que é decorado com o **HttpTriggerAttribute,** que define o comportamento do gatilho. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que devolve uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init e\_\_.py

*\_\_init e\_\_ .py* contém uma função `main()` Python que é desencadeada de acordo com a configuração em *função.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` conforme definido em *função.json*. `req` é um exemplo da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *função.json,* é um exemplo de [azure.functions.functions.httpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporta uma função que é desencadeada de acordo com a configuração em *função.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` conforme definido em *função.json*. O objeto de retorno, definido como `$return` em *função.json,* é a resposta. Para saber mais, consulte [funções Azure HTTP gatilhos e encadernações](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é desencadeada de acordo com a configuração em *função.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` do tipo **HttpRequest** conforme definido na *função.json*. O objeto de retorno, definido como `$return` em *função.json,* é a resposta. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* define um script de função que é desencadeado de acordo com a configuração em *função.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função recebe dados de pedido transmitidos para o `$Request` param definido em *função.json*. O objeto de retorno, definido como `Response` em *função.json,* é passado para o `Push-OutputBinding` cmdlet como resposta. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* é um ficheiro de configuração que define a entrada e a saída `bindings` para a função, incluindo o tipo de gatilho. 
::: zone-end

::: zone pivot="programming-language-python"
Pode alterar `scriptFile` invocar um ficheiro Python diferente, se desejar.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada de tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de apoio para a sua função

Antes de poder implementar o seu código de função para o Azure, precisa de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de função, que fornece o ambiente para executar o seu código de função. Uma aplicação de função mapeia o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Utilize os seguintes comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

1. Se ainda não o fez, inicie sessão no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`. (Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível a partir do comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Não é possível hospedar aplicações linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente chamado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou aplicação web, deve utilizar um grupo de recursos diferente.
    ::: zone-end  
    
1. Crie uma conta de armazenamento geral no seu grupo de recursos e região utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, substitua `<STORAGE_NAME>` por um nome globalmente único adequado a si. Os nomes devem conter três a 24 caracteres e apenas letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento incorre apenas alguns cêntimos (USD) para este arranque rápido.
    
1. Criar a aplicação Funções utilizando o [app de funções az criar](/cli/azure/functionapp#az-functionapp-create) comando. No exemplo seguinte, substitua `<STORAGE_NAME>` pelo nome da conta que usou no passo anterior e substitua `<APP_NAME>` por um nome globalmente único adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 

    ::: zone pivot="programming-language-python"  
    Se estiver a utilizar o Python 3.6, mude também `--runtime-version` para `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Se estiver a usar o Nó.js 8, mude também `--runtime-version` para `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado no âmbito do Plano de Consumo de [Funções Azure,](functions-scale.md#consumption-plan)que é gratuito pela quantidade de utilização que incorra aqui. O comando também prevê uma instância de Insights de Aplicação Azure associada no mesmo grupo de recursos, com o qual pode monitorizar a sua aplicação de função e visualizar registos. Para mais informações, consulte as [Funções Monitor Azure](functions-monitoring.md). A instância não incorre em custos até que o ative.
    
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para o Azure

::: zone pivot="programming-language-typescript"  
Antes de utilizar as Ferramentas Core para implementar o seu projeto para o Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

O seguinte comando prepara o seu projeto TypeScript para implementação:

```
npm run build:production 
```
::: zone-end  

Com os recursos necessários, está agora pronto para implementar o seu projeto de funções locais para a aplicação de funções em Azure, utilizando o comando [de publicação func azure functionapp.](functions-run-local.md#project-file-deployment) No exemplo seguinte, substitua `<APP_NAME>` pelo nome da sua aplicação.

```
func azure functionapp publish <APP_NAME>
```

Se vir o erro, "Não consigo encontrar a app com nome...", espere alguns segundos e tente novamente, já que o Azure pode não ter inicializado totalmente a app após o comando de `az functionapp create` anterior.

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

## <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido http para o seu URL no navegador ou com uma ferramenta como o curl. Em ambos os casos, o parâmetro URL `code` é a sua chave de [função](functions-bindings-http-webhook-trigger.md#authorization-keys) única que autoriza a invocação do ponto final da sua função.

# <a name="browser"></a>[Navegador](#tab/browser)

Copie o **URL invocado** completo mostrado na saída do comando de publicação numa barra de endereços de navegador, adiando o parâmetro de consulta `&name=Functions`. O navegador deve apresentar uma saída semelhante à que executou a função localmente.

![A saída da função é executada no Azure num browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[caracol](#tab/curl)

Executar [`curl`](https://curl.haxx.se/) com o **URL Invocação,** anexando o parâmetro `&name=Functions`. A saída do comando deve ser o texto, "Olá Funções".

![A saída da função funciona em Azure usando caracóis](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Para visualizar registos em tempo real para uma aplicação de função publicada, utilize o [Fluxo de Métricas ao Vivo](functions-monitoring.md#streaming-logs)insights da aplicação Insights .

## <a name="clean-up-resources"></a>Limpar recursos

Se continuar até ao próximo passo, Adicione uma ligação de saída de fila de [armazenamento Azure,](functions-add-output-binding-storage-queue-python.md)mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento azure](functions-add-output-binding-storage-queue-cli.md)
