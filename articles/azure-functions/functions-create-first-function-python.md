---
title: Criar uma função Python disparada por HTTP no Azure
description: Saiba como criar sua primeira função Python no Azure usando o Azure Functions Core Tools e o CLI do Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 791348088d909785b36934c3b9a2ae00fc0acbb7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622038"
---
# <a name="create-an-http-triggered-python-function-in-azure"></a>Criar uma função Python disparada por HTTP no Azure

Este artigo mostra como usar as ferramentas de linha de comando para criar um projeto Python que é executado no Azure Functions. Você também cria uma função que é disparada por uma solicitação HTTP. Por fim, você publica seu projeto para executar como uma [função sem servidor](functions-scale.md#consumption-plan) no Azure.

Este artigo é o primeiro dos dois guias de início rápido do Python para Azure Functions. Depois de concluir este guia de início rápido, você pode [Adicionar uma associação de saída de fila de armazenamento do Azure](functions-add-output-binding-storage-queue-python.md) à sua função.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve:

+ Instale o [Python 3.6.8](https://www.python.org/downloads/). Esta versão do Python é verificada com funções. 3,7 e versões posteriores ainda não têm suporte.

+ Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1575 ou uma versão posterior.

+ Instale o [CLI do Azure](/cli/azure/install-azure-cli) versão 2. x ou uma versão posterior.

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Criar e ativar um ambiente virtual (opcional)

Você deve usar um ambiente Python 3.6. x para desenvolver localmente funções Python. Execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`.

> [!NOTE]
> Se o Python não instalou o venv em sua distribuição do Linux, você pode instalá-lo usando o seguinte comando:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Raso

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou um prompt de comando do Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Agora que você ativou o ambiente virtual, execute os comandos restantes nele. Para sair do ambiente virtual, execute `deactivate`.

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Um projeto do Functions é o equivalente a um aplicativo de funções no Azure. Ele pode ter várias funções que compartilham as mesmas configurações locais e de hospedagem.

1. No ambiente virtual, execute o seguinte comando:

    ```console
    func init MyFunctionProj
    ```

1. Selecione **Python** como seu tempo de execução de trabalho.

    O comando cria uma pasta _MyFunctionProj_ . Ele contém estes três arquivos:

    * *local. Settings. JSON*: usado para armazenar configurações de aplicativo e cadeias de conexão ao executar localmente. Este arquivo não é publicado no Azure.
    * *requirements. txt*: contém a lista de pacotes que o sistema irá instalar na publicação no Azure.
    * *host. JSON*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Este arquivo é publicado no Azure.

1. Vá para a nova pasta *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Criar uma função

Adicione uma função ao novo projeto.

1. Para adicionar uma função ao seu projeto, execute o seguinte comando:

    ```console
    func new
    ```

1. Use a seta para baixo para selecionar o modelo de **gatilho http** .

1. Quando for solicitado um nome de função, insira *HttpTrigger* e pressione Enter.

Esses comandos criam uma subpasta chamada _HttpTrigger_. Ele contém os seguintes arquivos:

* *Function. JSON*: arquivo de configuração que define a função, o gatilho e outras associações. Observe que, nesse arquivo, o valor de `scriptFile` aponta para o arquivo que contém a função, e a matriz de `bindings` define o gatilho de invocação e as associações.

    Cada associação requer uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e a associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_. py*: arquivo de script que é sua função disparada por http. Observe que esse script tem um `main()`padrão. Os dados HTTP do gatilho passam para a função usando o `req` nomeado `binding parameter`. O `req`, que é definido em function. JSON, é uma instância da [classe Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido como `$return` em *Function. JSON*, é uma instância da [classe Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [Azure Functions gatilhos e associações http](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Executar localmente a função

A função é executada localmente usando o tempo de execução de Azure Functions.

1. Este comando inicia o aplicativo de funções:

    ```console
    func host start
    ```

    Quando o host de Azure Functions é iniciado, ele grava algo semelhante à saída a seguir. Ele está truncado aqui para que você possa ler melhor:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser.

1. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. A captura de tela a seguir mostra a resposta para a solicitação GET que a função local retorna ao navegador:

    ![Verificar localmente no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Selecione CTRL + C para desligar seu aplicativo de funções.

Agora que executou a função localmente, pode criar a aplicação de funções e outros recursos necessários no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Criar um aplicativo de funções no Azure

Um aplicativo de funções fornece um ambiente para executar seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Execute o comando a seguir. Substitua `<APP_NAME>` por um nome de aplicativo de funções exclusivo. Substitua `<STORAGE_NAME>` por um nome de conta de armazenamento. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure.

> [!NOTE]
> Você não pode hospedar aplicativos Linux e Windows no mesmo grupo de recursos. Se você tiver um grupo de recursos existente chamado `myResourceGroup` com um aplicativo de funções do Windows ou aplicativo Web, deverá usar um grupo de recursos diferente.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

O comando anterior também provisiona uma instância do Aplicativo Azure insights associada no mesmo grupo de recursos. Você pode usar essa instância para monitorar seu aplicativo de funções e exibir logs.

Agora você está pronto para publicar seu projeto do Functions local no aplicativo de funções no Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementar o projeto da aplicação de funções no Azure

Depois de criar o aplicativo de funções no Azure, você pode usar o comando Func ferramentas principais de [publicação do Azure functionapp](functions-run-local.md#project-file-deployment) para implantar o código do projeto no Azure. Neste exemplo, substitua `<APP_NAME>` pelo nome do seu aplicativo.

```console
func azure functionapp publish <APP_NAME> --build remote
```

A opção `--build remote` cria seu projeto Python remotamente no Azure por meio dos arquivos no pacote de implantação. 

Você verá uma saída semelhante à mensagem a seguir. Ele está truncado aqui para que você possa ler melhor:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Você pode copiar o valor de `Invoke url` para seu `HttpTrigger` e usá-lo para verificar sua função no Azure. A URL contém um `code` valor de cadeia de caracteres de consulta que é sua chave de função, o que dificulta para outras pessoas chamarem seu ponto de extremidade de gatilho HTTP no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Para exibir logs quase em tempo real para um aplicativo Python publicado, use o [Live Metrics Stream Application insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Passos seguintes

Você criou um projeto de funções do Python com uma função disparada por HTTP, a executa em seu computador local e a implantou no Azure. Agora, estenda sua função por...

> [!div class="nextstepaction"]
> [Adicionando uma associação de saída da fila de armazenamento do Azure](functions-add-output-binding-storage-queue-python.md)
