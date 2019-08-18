---
title: Criar uma função disparada por HTTP no Azure
description: Saiba como criar sua primeira função de Python no Azure com as ferramentas de núcleo de funções do Azure e a CLI do Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 47de61db96b0f8f9b338f135d4f32eecc4a64efe
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562937"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Criar uma função disparada por HTTP no Azure

Este artigo mostra como usar as ferramentas de linha de comando para criar um projeto Python que é executado no Azure Functions. A função que você cria é disparada por solicitações HTTP. Por fim, você publica seu projeto para executar como uma [função sem servidor](functions-scale.md#consumption-plan) no Azure.

Este artigo é o primeiro de dois guias de início rápido para Azure Functions. Depois de concluir este artigo, você [adicionará uma associação de saída de fila de armazenamento do Azure](functions-add-output-binding-storage-queue-python.md) à sua função.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve ter o seguinte:

+ Instale o [Python 3,6](https://www.python.org/downloads/).

+ Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.1071 ou uma versão posterior.

+ Instale o [CLI do Azure](/cli/azure/install-azure-cli) versão 2. x ou uma versão posterior.

+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Criar e ativar um ambiente virtual (opcional)

Para desenvolver e testar localmente funções Python, é recomendável usar um ambiente Python 3,6. Execute os seguintes comandos para criar e ativar um ambiente virtual com o nome `.venv`.

### <a name="bash"></a>Raso

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou um prompt de comando do Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Os comandos restantes são executados dentro do ambiente virtual.

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um projeto do Functions é o equivalente a um aplicativo de funções no Azure. Ele pode ter várias funções que compartilham as mesmas configurações locais e de hospedagem.

No ambiente virtual, execute o comando a seguir, escolhendo **Python** como seu tempo de execução de trabalho.

```console
func init MyFunctionProj
```

Uma pasta chamada _MyFunctionProj_ é criada, que contém os três seguintes arquivos:

* `local.settings.json`é usado para armazenar configurações de aplicativo e cadeias de conexão ao executar localmente. Este arquivo não é publicado no Azure.
* `requirements.txt`contém a lista de pacotes a serem instalados na publicação no Azure.
* `host.json`contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Este arquivo é publicado no Azure.

Navegue até a nova pasta MyFunctionProj:

```console
cd MyFunctionProj
```

Em seguida, você atualiza o arquivo host. JSON para habilitar os pacotes de extensão.  

## <a name="create-a-function"></a>Criar uma função

Para adicionar uma função ao seu projeto, execute o seguinte comando:

```console
func new
```

Escolha o modelo de **gatilho http** , `HttpTrigger` digite como o nome da função e pressione Enter.

Uma subpasta denominada _HttpTrigger_ é criada, que contém os seguintes arquivos:

* **Function. JSON**: arquivo de configuração que define a função, o gatilho e outras associações. Examine esse arquivo e veja que o valor de `scriptFile` aponta para o arquivo que contém a função, enquanto o gatilho de invocação e as associações são definidos `bindings` na matriz.

  Cada associação requer uma direção, um tipo e um nome exclusivo. O gatilho http tem uma associação de entrada do [`httpTrigger`](functions-bindings-http-webhook.md#trigger) tipo e a associação de [`http`](functions-bindings-http-webhook.md#output)saída do tipo.

* **init.py\_: arquivo de script que é sua função disparada por http.\_ \_ \_** Examine esse script e veja que ele contém um padrão `main()`. Os dados http do gatilho são passados para essa função usando o `req` parâmetro de associação nomeado. Definido em function. JSON, `req` é uma instância da [classe Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido `$return` como em function. JSON, é uma instância da [classe Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [Azure Functions gatilhos e associações http](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Executar localmente a função

O comando a seguir inicia o aplicativo de funções, que é executado localmente usando o mesmo Azure Functions tempo de execução que está no Azure.

```bash
func host start
```

Quando o host do Functions é iniciado, ele grava algo como a saída a seguir, que foi truncado para facilitar a leitura:

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função local:

![Testar localmente no browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Agora que executou a função localmente, pode criar a aplicação de funções e outros recursos necessários no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Criar um aplicativo de funções no Azure

Um aplicativo de funções fornece um ambiente para executar seu código de função. Permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Execute o seguinte comando utilizando um nome de aplicação de funções exclusivo em vez do `<APP_NAME>` marcador de posição e a conta de armazenamento de nomes para `<STORAGE_NAME>`. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Os aplicativos do Linux e do Windows não podem ser hospedados no mesmo grupo de recursos. Se você tiver um grupo de recursos existente `myResourceGroup` chamado com um aplicativo de funções do Windows ou aplicativo Web, deverá usar um grupo de recursos diferente.

Esse comando também provisionará uma instância do Aplicativo Azure insights associada no mesmo grupo de recursos que pode ser usada para monitorar e exibir logs.

Agora você está pronto para publicar seu projeto do Functions local no aplicativo de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Para exibir logs quase em tempo real para um aplicativo Python publicado, é recomendável usar o [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs)

## <a name="next-steps"></a>Passos Seguintes

Você criou um projeto de funções do Python com uma função disparada por HTTP, a executa em seu computador local e a implantou no Azure. Agora, estenda sua função por...

> [!div class="nextstepaction"]
> [Adicionando uma associação de saída da fila de armazenamento do Azure](functions-add-output-binding-storage-queue-python.md)
