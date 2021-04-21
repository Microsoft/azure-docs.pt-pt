---
title: Criar uma função Python a partir da linha de comando para Funções Azure
description: Aprenda a criar uma função Python a partir da linha de comando e publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e144304ae1b36ca02d4b8796e7994e87b09505d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831101"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função Python em Azure a partir da linha de comando

> [!div class="op_single_selector" title1="Selecione o seu idioma de função: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Neste artigo, utiliza ferramentas de linha de comando para criar uma função Python que responde a pedidos HTTP. Depois de testar o código localmente, você implantá-lo para o <abbr title="Um ambiente de computação em tempo de execução em que todos os detalhes do servidor são transparentes para os desenvolvedores de aplicações, o que simplifica o processo de implementação e gestão do código.">sem servidor</abbr> ambiente de <abbr title="Um serviço Azure que fornece um ambiente de computação sem servidor de baixo custo para aplicações.">Funções do Azure</abbr>.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em Código visual](create-first-function-vs-code-python.md) do Estúdio deste artigo.

## <a name="1-configure-your-environment"></a>1. Configurar o seu ambiente

Antes de começar, deve ter o seguinte:

+ Um Azure <abbr title="O perfil que mantém informações de faturação para o uso do Azure.">conta</abbr> com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2) 
  
+ Ou o <abbr title="Um conjunto de ferramentas de linha de comando cross-platform para trabalhar com os recursos Azure a partir do seu computador de desenvolvimento local, como alternativa à utilização do portal Azure.">CLI do Azure</abbr> ou <abbr title="Um módulo PowerShell que fornece comandos para trabalhar com recursos Azure a partir do seu computador de desenvolvimento local, como alternativa à utilização do portal Azure.">Azure PowerShell</abbr> para a criação de recursos Azure:

    + Versão 2.4 ou posterior do [Azure CLI.](/cli/azure/install-azure-cli)

    + [Versão Azure PowerShell](/powershell/azure/install-az-ps) 5.0 ou posterior.

+ [Python 3.8 (64-bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), que são todos suportados pela versão 3.x de Funções Azure.

### <a name="11-prerequisite-check"></a>1.1 Verificação prévia

Verifique os seus pré-requisitos, que dependem se está a utilizar o Azure CLI ou o Azure PowerShell para criar recursos Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Em um terminal ou janela de comando, correr `func --version` para verificar que o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com funções Azure no seu computador local.">Azure Functions Core Tools</abbr> são a versão 3.x.

+ Corra `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ Corra `az login` para iniciar seduca no Azure e verifique uma subscrição ativa.

+ Executar `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.8.x, 3.7.x ou 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Em um terminal ou janela de comando, correr `func --version` para verificar que o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com funções Azure no seu computador local.">Azure Functions Core Tools</abbr> são a versão 3.x.

+ Executar `(Get-Module -ListAvailable Az).Version` e verificar a versão 5.0 ou mais tarde. 

+ Corra `Connect-AzAccount` para iniciar seduca no Azure e verifique uma subscrição ativa.

+ Executar `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.8.x, 3.7.x ou 3.6.x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Criar e ativar um ambiente virtual

Numa pasta adequada, executar os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv` . Certifique-se de que utiliza Python 3.8, 3.7 ou 3.6, que são suportados por Funções Azure.

# <a name="bash"></a>[festa](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se python não instalou o pacote venv na sua distribuição Linux, execute o seguinte comando:

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

Você dirige todos os comandos subsequentes neste ambiente virtual ativado. 

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Criar um projeto de função local

Nesta secção, você cria um local <abbr title="Um recipiente lógico para uma ou mais funções individuais que podem ser implantadas e geridas em conjunto.">Projeto Azure Functions</abbr> em Python. Cada função no projeto responde a um específico <abbr title="O tipo de evento que invoca o código da função, como um pedido HTTP, uma mensagem de fila ou uma hora específica.">gatilho</abbr>.

1. Executar o `func init` comando para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navegue na pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>O que é criado na pasta LocalFunctionProj?</strong></summary>
    
    Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsligados .](functions-host-json.md) Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*
    </details>

1. Adicione uma função ao seu projeto utilizando o seguinte comando:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    O `--name` argumento é o nome único da sua função (HttpExample).

    O `--template` argumento especifica o gatilho da função (HTTP).
    
    `func new`cria uma sub-dobradeira que corresponde ao nome da função que contém um ficheiro *\_ \_ \_ \_ init .py* com o código da função e um ficheiro de configuração nomeadofunction.js *em*.

    <br/>    
    <details>
    <summary><strong>Código para __.py init__</strong></summary>
    
    *\_ \_ init \_ \_ .py* contém uma `main()` função Python que é desencadeada de acordo com a configuração em *function.jsem*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Para um gatilho HTTP, a função recebe dados de pedido na variável `req` tal como definida emfunction.js *em*. `req` é um exemplo da [classe azure.funções.HttpRequest class](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.js,* é um exemplo de [azure.functions.httpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Código para function.jsem</strong></summary>

    *function.jsem é* um ficheiro de configuração que define o <abbr title="Ligações declarativas entre uma função e outros recursos. Uma ligação de entrada fornece dados para a função; uma ligação de saída fornece dados da função para outros recursos.">encadernações de entrada e saída</abbr> para a função, incluindo o tipo de gatilho.
    
    Pode alterar `scriptFile` para invocar um ficheiro Python diferente, se desejar.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e da ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md) .    
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

1. O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando terminar, use <kbd>Ctrl+C</kbd> e escolha <kbd>y</kbd> para parar o hospedeiro de funções.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Crie recursos de suporte Azure para a sua função

Antes de poder implementar o seu código de função para Azure, tem de criar um <abbr title="Um recipiente lógico para recursos Azure relacionados que você pode gerir como uma unidade.">grupo de recursos</abbr>, a <abbr title="Uma conta que contém todos os seus objetos de dados de armazenamento Azure. A conta de armazenamento fornece um espaço de nome único para os seus dados de armazenamento.">conta de armazenamento</abbr>, e um <abbr title="O recurso em nuvem que acolhe funções sem servidor no Azure, que fornece o ambiente computacional subjacente no qual as funções são executadas.">app de função</abbr> Utilizando os seguintes comandos:

1. Se ainda não o fez, inscreva-se no Azure:

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

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um grupo de recursos. Você geralmente cria o seu grupo de recursos e recursos em uma região próxima de você, usando uma região disponível devolvida do cmdlet [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

    Não é possível hospedar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente nomeado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.

1. Crie uma conta de Armazenamento Azure de uso geral no seu grupo de recursos e região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    A [conta de armazenamento az cria](/cli/azure/storage/account#az_storage_account_create) o comando cria a conta de armazenamento. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    O [Cmdlet New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento.

    ---

    Substitua `<STORAGE_NAME>` por um nome que lhe seja adequado e <abbr title="O nome deve ser único em todas as contas de armazenamento utilizadas por todos os clientes da Azure em todo o mundo. Por exemplo, pode utilizar uma combinação do seu nome pessoal ou da empresa, nome de aplicação e um identificador numérico, como em contosobizappstorage20.">único em Azure Storage</abbr>. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).
    
    A conta de armazenamento incorre apenas alguns cêntimos (USD) para este arranque rápido.

1. Crie a aplicação de função em Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    O comando [az functionapp cria](/cli/azure/functionapp#az_functionapp_create) a aplicação de função em Azure. Se estiver a utilizar python 3.7 ou 3.6, mude `--runtime-version` para `3.7` `3.6` ou, respectivamente.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    O [cmdlet New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria a aplicação de função em Azure. Se estiver a utilizar python 3.7 ou 3.6, mude `-RuntimeVersion` para `3.7` `3.6` ou, respectivamente.

    ---
    
    `<STORAGE_NAME>`Substitua-o pelo nome da conta que utilizou no passo anterior.

    Substitua `<APP_NAME>` por um <abbr title="Um nome que deve ser único em todos os clientes da Azure em todo o mundo. Por exemplo, você pode usar uma combinação do seu nome pessoal ou de organização, nome de aplicação e um identificador numérico, como em contoso-bizapp-func-20.">nome globalmente único apropriado para si</abbr>. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
    
    <br/>
    <details>
    <summary><strong>Qual é o custo dos recursos a provisionados em Azure?</strong></summary>

    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado ao abrigo do Plano de [Consumo de Funções Azure](functions-scale.md#overview-of-plans), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implementar o projeto de função para Azure

Depois de ter criado com sucesso a sua aplicação de função em Azure, está agora pronto para **implementar o seu projeto de funções locais** utilizando o comando de publicação do [func azure functionapp.](functions-run-local.md#project-file-deployment)  

No exemplo seguinte, `<APP_NAME>` substitua-a pelo nome da sua aplicação.

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

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido HTTP ao seu URL no navegador ou com uma ferramenta como <abbr title="Uma ferramenta de linha de comando para gerar pedidos HTTP a um URL; ver https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Browser](#tab/browser)

Copie o **URL completo do Invoke** mostrado na saída do comando numa barra de `publish` endereços do navegador, anexando o parâmetro de consulta **&nome=Funções**. O navegador deve apresentar uma saída semelhante à de quando executou a função localmente.

![A saída da função executada no Azure num browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Executar [`curl`](https://curl.haxx.se/) com o URL **invocado,** anexando o parâmetro **&nome=Funções**. A saída do comando deve ser o texto"Olá Funções".

![A saída da função executada em Azure usando o caracol](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Ver registos de streaming em tempo real

Executar o seguinte comando para visualizar [registos](functions-run-local.md#enable-streaming-logs) de streaming em tempo real em Insights de Aplicação no portal Azure:

```console
func azure functionapp logstream <APP_NAME> --browser
```

`<APP_NAME>`Substitua-o pelo nome da sua aplicação de função.

Numa janela de terminal separada ou no browser, volte a ligar para a função remota. Um registo verboso da execução da função em Azure é mostrado no terminal. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Limpar recursos

Se continuar a seguir o [próximo passo](#next-steps) e adicionar um <abbr title="Um meio de associar uma função a uma fila de armazenamento, para que possa criar mensagens na fila. ">Encadernação de saída da fila de armazenamento Azure</abbr>Mantenha todos os seus recursos no lugar, pois construirá o que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

 # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/python-functions-qs-survey)
