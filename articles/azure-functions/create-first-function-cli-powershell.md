---
title: Criar uma função PowerShell a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função PowerShell a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937234"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função PowerShell em Azure a partir da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, utiliza ferramentas de linha de comando para criar uma função PowerShell que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em Código visual](create-first-function-vs-code-powershell.md) do Estúdio deste artigo.

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)

+ Uma das seguintes ferramentas para a criação de recursos Azure:

    + [Versão Azure PowerShell](/powershell/azure/install-az-ps) 5.0 ou posterior.

    + Versão 2.4 ou posterior do [Azure CLI.](/cli/azure/install-azure-cli)

+ O [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Verificação pré-requisito

Verifique os seus pré-requisitos, que dependem se está a utilizar o Azure CLI ou o Azure PowerShell para criar recursos Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 3.x.

+ Corra `az --version` para verificar se a versão Azure CLI é 2.4 ou mais tarde.

+ Corra `az login` para iniciar seduca no Azure e verifique uma subscrição ativa.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 3.x.

+ Executar `(Get-Module -ListAvailable Az).Version` e verificar a versão 5.0 ou mais tarde. 

+ Corra `Connect-AzAccount` para iniciar seduca no Azure e verifique uma subscrição ativa.

---

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

1. Executar o `func init` comando, da seguinte forma, para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:  

    ```console
    func init LocalFunctionProj --powershell
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

#### <a name="runps1"></a>run.ps1

*run.ps1* define um script de função que é desencadeado de acordo com a configuração em *function.jsem*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função recebe dados de pedido passados para o `$Request` param definido em *function.jsem*. O objeto de retorno, definido como `Response` em *function.js,* é passado para o `Push-OutputBinding` cmdlet como resposta. 

#### <a name="functionjson"></a>function.json

*function.jsligado* é um ficheiro de configuração que define a entrada e saída `bindings` para a função, incluindo o tipo de gatilho. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e da ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Crie a aplicação de função em Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    O comando [az functionapp cria](/cli/azure/functionapp#az_functionapp_create) a aplicação de função em Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    O [cmdlet New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria a aplicação de função em Azure. 
    
    ---
    
    No exemplo anterior, `<STORAGE_NAME>` substitua-o pelo nome da conta que usou no passo anterior e substitua-o `<APP_NAME>` por um nome globalmente único, adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
    
    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado ao abrigo do Plano de [Consumo de Funções Azure](consumption-plan.md), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure]

[Ligue-se a uma fila de armazenamento Azure]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
