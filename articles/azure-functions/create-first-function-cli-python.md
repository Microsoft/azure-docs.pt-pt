---
title: Criar uma função Python a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função Python a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 0aea0425123cab4da1939e85a2cfd6071f772737
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95795759"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função Python em Azure a partir da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, utiliza ferramentas de linha de comando para criar uma função Python que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em Código visual](create-first-function-vs-code-python.md) do Estúdio deste artigo.

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)
  
+ Uma das seguintes ferramentas para a criação de recursos Azure:

    + Versão 2.4 ou posterior do [Azure CLI.](/cli/azure/install-azure-cli)

    + [Versão Azure PowerShell](/powershell/azure/install-az-ps) 5.0 ou posterior.

+ [Python 3.8 (64-bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), que são todos suportados pela versão 3.x de Funções Azure.

### <a name="prerequisite-check"></a>Verificação pré-requisito

Verifique os seus pré-requisitos, que dependem se está a utilizar o Azure CLI ou o Azure PowerShell para criar recursos Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 3.x.

+ Corra `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ Corra `az login` para iniciar seduca no Azure e verifique uma subscrição ativa.

+ Executar `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.8.x, 3.7.x ou 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 3.x.

+ Executar `(Get-Module -ListAvailable Az).Version` e verificar a versão 5.0 ou mais tarde. 

+ Corra `Connect-AzAccount` para iniciar seduca no Azure e verifique uma subscrição ativa.

+ Executar `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.8.x, 3.7.x ou 3.6.x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Criar e ativar um ambiente virtual

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

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

1. Executar o `func init` comando, da seguinte forma, para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navegue na pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```
    
    Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsligados .](functions-host-json.md) Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o `--name` argumento é o nome único da sua função (HttpExample) e o argumento especifica o gatilho da `--template` função (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` cria uma sub-dobradeira que corresponde ao nome da função que contém um ficheiro de código adequado à língua escolhida do projeto e um ficheiro de configuração denominado *function.jsem*.

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examine o conteúdo do ficheiro

Se desejar, pode saltar para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

#### <a name="__init__py"></a>\_\_.py \_ init \_

*\_ \_ init \_ \_ .py* contém uma `main()` função Python que é desencadeada de acordo com a configuração em *function.jsem*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` tal como definida emfunction.js *em*. `req` é um exemplo da [classe azure.funções.HttpRequest class](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.js,* é um exemplo de [azure.functions.httpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [as funções Azure HTTP gatilhos e encadernações](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.jsligado* é um ficheiro de configuração que define a entrada e saída `bindings` para a função, incluindo o tipo de gatilho.

Pode alterar `scriptFile` para invocar um ficheiro Python diferente, se desejar.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e da ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de suporte para a sua função

Antes de poder implantar o seu código de função para Azure, tem de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função mapeia para o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão, implementação e partilha mais fácil de recursos.

Utilize os seguintes comandos para criar estes itens. Tanto o Azure CLI como o PowerShell são apoiados.

1. Se ainda não o fez, inscreva-se no Azure:

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

1. Criar um grupo de recursos nomeado `AzureFunctionsQuickstart-rg` na `westeurope` região. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O [grupo az cria](/cli/azure/group#az-group-create) um grupo de recursos. Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível devolvida do `az account list-locations` comando.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um grupo de recursos. Você geralmente cria o seu grupo de recursos e recursos em uma região próxima de você, usando uma região disponível devolvida do cmdlet [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation)

    ---

    > [!NOTE]
    > Não é possível hospedar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente nomeado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.

1. Crie uma conta de armazenamento para fins gerais no seu grupo de recursos e região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    A [conta de armazenamento az cria](/cli/azure/storage/account#az-storage-account-create) o comando cria a conta de armazenamento. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    O [Cmdlet New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento.

    ---

    No exemplo anterior, `<STORAGE_NAME>` substitua-o por um nome que lhe seja adequado e único no Azure Storage. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](storage-considerations.md#storage-account-requirements).
    
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
    
    No exemplo anterior, `<STORAGE_NAME>` substitua-o pelo nome da conta que usou no passo anterior e substitua-o `<APP_NAME>` por um nome globalmente único, adequado a si.  O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
    
    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado ao abrigo do Plano de [Consumo de Funções Azure](functions-scale.md#consumption-plan), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Executar o seguinte comando para visualizar [registos](functions-run-local.md#enable-streaming-logs) de streaming em tempo real em Insights de Aplicação no portal Azure:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Numa janela de terminal separada ou no browser, volte a ligar para a função remota. Um registo verboso da execução da função em Azure é mostrado no terminal. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/python-functions-qs-survey)
