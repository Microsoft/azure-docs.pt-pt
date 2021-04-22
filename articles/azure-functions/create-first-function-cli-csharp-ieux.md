---
title: Criar uma função C# a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função C# a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 45173a74c0e3189c1f356aea2f8024ff15409f32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866202"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função C# em Azure a partir da linha de comando

> [!div class="op_single_selector" title1="Selecione o seu idioma de função: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Neste artigo, utiliza ferramentas de linha de comando para criar uma função baseada na biblioteca de classe C# que responda a pedidos HTTP. Depois de testar o código localmente, você implantá-lo para o <abbr title="Um ambiente de computação em tempo de execução em que todos os detalhes do servidor são transparentes para os desenvolvedores de aplicações, o que simplifica o processo de implementação e gestão do código.">sem servidor</abbr> ambiente de <abbr title="O serviço da Azure que fornece um ambiente de computação sem servidor de baixo custo para aplicações.">Funções do Azure</abbr>.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em Código visual](create-first-function-vs-code-csharp.md) do Estúdio deste artigo.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

+ Obter um Azure <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">conta</abbr> com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associado a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Instalar [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

+ Instale a versão 3.x [das Funções Azure.](functions-run-local.md#v2)

+ Ou o <abbr title="Um conjunto de ferramentas de linha de comando cross-platform para trabalhar com os recursos Azure a partir do seu computador de desenvolvimento local, como alternativa à utilização do portal Azure.">CLI do Azure</abbr> ou <abbr title="Um módulo PowerShell que fornece comandos para trabalhar com recursos Azure a partir do seu computador de desenvolvimento local, como alternativa à utilização do portal Azure.">Azure PowerShell</abbr> para a criação de recursos Azure:

    + Versão 2.4 ou posterior do [Azure CLI.](/cli/azure/install-azure-cli)

    + [Versão Azure PowerShell](/powershell/azure/install-az-ps) 5.0 ou posterior.

---

### <a name="2-verify-prerequisites"></a>2. Verificar os pré-requisitos

Verifique os seus pré-requisitos, que dependem se está a utilizar o Azure CLI ou o Azure PowerShell para criar recursos Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Em um terminal ou janela de comando, correr `func --version` para verificar que o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com funções Azure no seu computador local.">Azure Functions Core Tools</abbr> são a versão 3.x.

+ **Correr** `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ **Correr** `az login` para iniciar seduca e verificar uma subscrição ativa.

+ **Correr** `dotnet --list-sdks` para verificar se a versão 3.1.x do Núcleo NET SDK está instalada

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Correr** `func --version` para verificar se as Ferramentas Principais de Funções Azure são a versão 3.x.

+ **Correr** `(Get-Module -ListAvailable Az).Version` e verificar a versão 5.0 ou mais tarde. 

+ **Correr** `Connect-AzAccount` para iniciar seduca e verificar uma subscrição ativa.

+ **Correr** `dotnet --list-sdks` para verificar se a versão 3.1.x do Núcleo NET SDK está instalada

---

## <a name="3-create-a-local-function-project"></a>3. Criar um projeto de função local

Nesta secção, você cria um local <abbr title="Um recipiente lógico para uma ou mais funções individuais que podem ser implantadas e geridas em conjunto.">Projeto Azure Functions</abbr> em C#. Cada função no projeto responde a um específico <abbr title="Um evento que invoca o código da função, como um pedido HTTP, uma mensagem de fila ou uma hora específica.">gatilho</abbr>.

1. Executar o `func init` comando para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Correr** 'cd LocalFunctionProj' para navegar para o <abbr title="Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados local.settings.jse host.jsligados. Como local.settings.jspode conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro .gitignore.">pasta de projeto</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Adicione uma função ao seu projeto utilizando o seguinte comando:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    O `--name` argumento é o nome único da sua função (HttpExample).

    O `--template` argumento especifica o gatilho da função (HTTP).

    
    <br/>   
    <details>  
    <summary><strong>Opcional: Código para HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* contém um `Run` método que recebe dados de pedido na `req` variável é um [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) que está decorado com o **HttpTriggerAttribute,** que define o comportamento do gatilho.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que devolve uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=csharp).  
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Executar a função localmente

1. Executar a sua função iniciando o anfitrião local do tempo de execução Azure Functions a partir da pasta *LocalFunctionProj:*

    ```
    func start
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

    <br/>
    <details>
    <summary><strong>Não vejo httpExample na saída</strong></summary>

    Se httpExample não aparecer, é provável que tenha iniciado o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize <kbd>ctrl+C</kbd> para parar o hospedeiro, navegue na pasta raiz do projeto e volte a executar o comando anterior.
    </details>

1. Copie o URL da sua função **HttpExample** desta saída para um browser e apencha a cadeia de consulta **?name=<YOUR_NAME>,** tornando o URL completo como **http://localhost:7071/api/HttpExample?name=Functions** . O navegador deve exibir uma mensagem como **Hello Functions**:

    ![Resultado da função executado localmente no navegador](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. Selecione  <kbd>Ctrl+C</kbd> e escolha <kbd>y</kbd> para parar o anfitrião de funções.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Crie recursos de suporte Azure para a sua função

Antes de poder implementar o seu código de função para Azure, tem de criar um <abbr title="Um recipiente lógico para recursos Azure relacionados que você pode gerir como uma unidade.">grupo de recursos</abbr>, a <abbr title="Uma conta que contém todos os seus objetos de dados de armazenamento Azure. A conta de armazenamento fornece um espaço de nome único para os seus dados de armazenamento.">conta de armazenamento</abbr>, e um <abbr title="O recurso em nuvem que acolhe funções sem servidor no Azure, que fornece o ambiente computacional subjacente no qual as funções são executadas.">app de função</abbr> Utilizando os seguintes comandos:

1. Se ainda não o fez, inscreva-se no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


    ---

1. Criar um grupo de recursos nomeado `AzureFunctionsQuickstart-rg` na `westeurope` região. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    O [grupo az cria](/cli/azure/group#az_group_create) um grupo de recursos. Geralmente cria o seu grupo de recursos e recursos num <abbr title="Uma referência geográfica a um centro de dados específico do Azure no qual os recursos são atribuídos.">region</abbr> perto de si, usando uma região disponível devolvida do `az account list-locations` comando.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```


    ---

    Não é possível hospedar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente nomeado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.
    
1. Crie uma conta de Armazenamento Azure de uso geral no seu grupo de recursos e região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Substitua `<STORAGE_NAME>` por um nome que lhe seja adequado e <abbr title="O nome deve ser único em todas as contas de armazenamento utilizadas por todos os clientes da Azure em todo o mundo. Por exemplo, pode utilizar uma combinação do seu nome pessoal ou da empresa, nome de aplicação e um identificador numérico, como em contosobizappstorage20">único em Azure Storage</abbr>. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).


1. Crie a aplicação de função em Azure.
**Substituir** '<STORAGE_NAME>** com nome no passo anterior.
**Substituir** "<APP_NAME>" com um nome globalmente único.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    `<STORAGE_NAME>`Substitua-o pelo nome da conta que utilizou no passo anterior.

    Substitua `<APP_NAME>` por um <abbr title="Um nome que deve ser único em todos os clientes da Azure em todo o mundo. Por exemplo, você pode usar uma combinação do seu nome pessoal ou de organização, nome de aplicação e um identificador numérico, como em contoso-bizapp-func-20.">nome único</abbr>. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 

    <br/>
    <details>
    <summary><strong>Qual é o custo dos recursos a provisionados em Azure?</strong></summary>

    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado no âmbito do [plano de consumo de funções Azure](consumption-plan.md), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implementar o projeto de função para Azure


**Cópia** ' func azure funtionapp publicar <APP_NAME>  no seu terminal `<APP_NAME>` Substitua-o pelo nome da sua aplicação.
**Executar**

```console
func azure functionapp publish <APP_NAME>
```

O `publish` comando apresenta resultados semelhantes à seguinte saída (truncada para a simplicidade):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Invocar a função em Azure

Copie o **URL completo do Invoke** mostrado na saída do `publish` comando numa barra de endereços do navegador. **Anexar** o parâmetro de consulta **&nome=Funções**. 

![A saída da função executada no Azure num browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Limpar recursos

Se continuar até ao [próximo passo](#next-steps) e adicionar uma saída de fila de armazenamento Azure <abbr title="Uma ligação declarativa entre uma função e outros recursos. Uma ligação de entrada fornece dados para a função; uma ligação de saída fornece dados da função para outros recursos.">encadernação</abbr>Mantenha todos os seus recursos no lugar, pois construirá o que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
