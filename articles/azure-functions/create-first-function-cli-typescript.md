---
title: Criar uma função TypeScript a partir da linha de comando - Funções Azure
description: Aprenda a criar uma função TypeScript a partir da linha de comando e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937200"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Quickstart: Criar uma função TypeScript em Azure a partir da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, utiliza ferramentas de linha de comando para criar uma função TypeScript que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em Código visual](create-first-function-vs-code-typescript.md) do Estúdio deste artigo.

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)

+ Uma das seguintes ferramentas para a criação de recursos Azure:

    + Versão 2.4 ou posterior do [Azure CLI.](/cli/azure/install-azure-cli)

    + [Versão Azure PowerShell](/powershell/azure/install-az-ps) 5.0 ou posterior.

+ [Node.js](https://nodejs.org/), Versões LTS ativas e de manutenção (8.11.1 e 10.14.1 recomendado).

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
    func init LocalFunctionProj --typescript
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

#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é desencadeada de acordo com a configuração em *function.jsem*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função recebe dados de pedido na variável `req` do tipo **HttpRequest,** tal como definido em *function.jsem*. O objeto de retorno, definido como `$return` em *function.js,* é a resposta. 

#### <a name="functionjson"></a>function.json

*function.jsligado* é um ficheiro de configuração que define a entrada e saída `bindings` para a função, incluindo o tipo de gatilho. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma ligação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e da ligação de saída do tipo [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Executar a sua função iniciando o anfitrião local do tempo de execução Azure Functions a partir da pasta *LocalFunctionProj:*

    ```console
    npm install
    npm start
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
    
    >[!NOTE]  
    > Se httpExample não aparecer como mostrado abaixo, é provável que tenha iniciado o hospedeiro de fora da pasta raiz do projeto. Nesse caso, utilize **o Ctrl** + **C** para parar o hospedeiro, navegue na pasta raiz do projeto e volte a executar o comando anterior.

1. Copie o URL da sua `HttpExample` função desta saída para um browser e apencha a cadeia de `?name=<your-name>` consulta, fazendo com que o URL completo seja semelhante `http://localhost:7071/api/HttpExample?name=Functions` a . O navegador deve exibir uma mensagem `Hello Functions` como:

    ![Resultado da função executado localmente no navegador](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    O terminal em que iniciou o seu projeto também mostra a saída de registo à medida que faz pedidos.

1. Quando estiver pronto, use **o Ctrl** + **C** e opte por `y` parar o hospedeiro de funções.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Crie a aplicação de função em Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    O comando [az functionapp cria](/cli/azure/functionapp#az_functionapp_create) a aplicação de função em Azure. Se estiver a usar Node.js 10, também mude `--runtime-version` para `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    O [cmdlet New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria a aplicação de função em Azure. Se estiver a usar Node.js 10, mude `-RuntimeVersion` para `10` .

    ---
        
    No exemplo anterior, `<STORAGE_NAME>` substitua-o pelo nome da conta que usou no passo anterior e substitua-o `<APP_NAME>` por um nome globalmente único, adequado a si. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. 
    
    Este comando cria uma aplicação de função em execução no seu tempo de funcionamento de idioma especificado ao abrigo do Plano de [Consumo de Funções Azure](consumption-plan.md), que é gratuito para a quantidade de utilização que incorre aqui. O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.

## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para Azure

Antes de utilizar as Ferramentas Core para implantar o seu projeto no Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

1. Utilize o seguinte comando para preparar o seu projeto TypeScript para a implementação:

    ```console
    npm run build:production 
    ```

1. Com os recursos necessários, está agora pronto para implementar o seu projeto de funções locais para a aplicação de funções em Azure, utilizando o comando [de publicação do func azure functionapp.](functions-run-local.md#project-file-deployment) No exemplo seguinte, `<APP_NAME>` substitua-a pelo nome da sua aplicação.

    ```console
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

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
