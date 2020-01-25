---
title: Adicione uma ligação de fila de armazenamento Azure à sua função Python
description: Integre uma fila de armazenamento Azure com uma função Python utilizando uma ligação de saída.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 14a381d13da052fd67679ed17bbb6b6711f7a0e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715366"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicione uma ligação de fila de armazenamento Azure à sua função Python

Neste artigo, integra uma fila de armazenamento Azure com a função e conta de armazenamento que criou na [Create a HTTP triggered Python função](functions-create-first-function-python.md). Obtém esta integração utilizando uma *ligação* de saída que escreve dados de um pedido http para uma mensagem na fila. A conclusão deste artigo não incorre em custos adicionais para além dos poucos cêntimos de USD do quickstart anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o quickstart, [Crie uma função De Python ativada](functions-create-first-function-python.md)em HTTP . Se já limpou os recursos no final desse artigo, passe novamente pelos passos para recriar a app Funções em Azure, mas deixe os recursos no lugar.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recupere a cadeia de ligação de armazenamento azure

Quando criou uma aplicação de função em Azure no quickstart anterior, também criou uma conta de Armazenamento. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Ao descarregar a definição no ficheiro *local.settings.json,* pode utilizar essa ligação escrever para uma fila de armazenamento na mesma conta ao executar a função localmente. 

1. A partir da raiz do projeto, execute o seguinte comando, substituindo `<app_name>` com o nome da sua aplicação de função a partir do quickstart anterior. Este comando irá substituir quaisquer valores existentes no ficheiro.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor denominado `AzureWebJobsStorage`, que é a cadeia de ligação da conta de armazenamento. Usa o nome `AzureWebJobsStorage` e a cadeia de ligação noutras secções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json contém segredos descarregados* do Azure, exclua sempre este ficheiro do controlo de origem. O ficheiro *.gitignore* criado com um projeto de funções locais exclui o ficheiro por defeito.

## <a name="add-an-output-binding-to-functionjson"></a>Adicione uma ligação de saída ao função.json

Embora uma função possa ter apenas um gatilho, pode ter várias ligações de entrada e saída, que permitem ligar-se a outros serviços e recursos Azure sem escrever código de integração personalizado. Declara estas encadernações no ficheiro *função.json* na sua pasta de funções como adequado para o idioma que está a utilizar para a função.

Desde o quickstart anterior, o ficheiro *function.json* na pasta *HttpExample* contém duas ligações na coleção `bindings`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Cada encadernação tem pelo menos um tipo, uma direção e um nome. No exemplo acima, a primeira ligação é de tipo `httpTrigger` com a direção `in`. Para a direção `in`, `name` especifica o nome de um parâmetro de entrada enviado para a função quando invocado pelo gatilho.

A segunda ligação é de tipo `http` com a direção `out`, caso em que o `name` especial de `$return` indica que esta ligação utiliza o valor de retorno da função em vez de fornecer um parâmetro de entrada.

Para escrever a uma fila de armazenamento azure a partir desta função, adicione uma ligação `out` de tipo `queue` com o nome `msg`, como indicado no código abaixo:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Neste caso, `msg` é dada à função como argumento de saída. Para um tipo `queue`, deve também especificar o nome da fila em `queueName` e fornecer o *nome* da ligação De armazenamento Azure (a partir de *local.settings.json*) em `connection`.

Para obter mais informações sobre os detalhes das ligações, consulte as [Funções Azure acionam e encaderna conceitos](functions-triggers-bindings.md) e [configuração](functions-bindings-storage-queue.md#output---configuration)de saída de fila .

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para usar a ligação de saída

Com a ligação da fila especificada na *função.json,* pode agora atualizar a sua função para receber o parâmetro de saída `msg` e escrever mensagens para a fila.

Update *HttpExample\\\_\_init\_\_.py* para combinar com o seguinte código, adicionando o parâmetro `msg` à definição de função e `msg.set(name)` sob a declaração `if name:`.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

O parâmetro `msg` é um exemplo do [`azure.functions.InputStream class`. ](/python/api/azure-functions/azure.functions.httprequest) O seu método `set` escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.

Observe que *não* precisa de escrever nenhum código para autenticação, obter uma referência na fila ou escrever dados. Todas estas tarefas de integração são convenientemente tratadas no tempo de funcionamento das Funções Azure e na ligação de saída da fila.

## <a name="run-and-test-the-function-locally"></a>Executar e testar a função localmente

1. Num aviso de terminal ou comando, navegue para a pasta do projeto de função, *LocalFunctionProj*.

1. Inicie o hospedeiro de funcionamento das Funções Azure utilizando o seguinte comando.

    ```
    func host start
    ```

1. Uma vez que o arranque esteja completo e você vê o URL para o `HttpExample` ponto final, copie o seu URL para um navegador e apreenda a `?name=<your-name>`de cadeia de consulta, tornando o URL completo como `http://localhost:7071/api/HttpExample?name=Guido`. O navegador deve apresentar uma mensagem como `Hello Guido` como no artigo anterior.

    Se não vir o ponto final `HttpExample` aparecer, pare o hospedeiro com **Ctrl**+**C** e verifique se a saída está a verificar se há erros. Por exemplo, o hospedeiro não ativará o ponto final se houver um erro na *função.json*. Verifique também se está a executar `func host start` a partir da pasta do projeto de funções e não da pasta *HttpExample.*

1. Quando terminar, pare o hospedeiro com **Ctrl**+**C**.

> [!TIP]
> Durante o arranque, o anfitrião descarrega e instala a [extensão de ligação](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) de Armazenamento e outras extensões de ligação da Microsoft. Esta instalação ocorre porque as extensões de ligação são ativadas por padrão no ficheiro *host.json* com as seguintes propriedades:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se encontrar erros relacionados com extensões de ligação, verifique se as propriedades acima estão presentes no *host.json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila do Armazenamento Azure

Quando a sua função gera uma resposta HTTP para o navegador web, também chama `msg.set(name)`, que escreve uma mensagem para uma fila de Armazenamento Azure chamada `outqueue`, conforme especificado na ligação da fila. Pode ver a fila no [portal Azure](../storage/queues/storage-quickstart-queues-portal.md) ou no [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Pode também ver a fila no Azure CLI conforme descrito nos seguintes passos:

1. Abra o ficheiro *local.definição.json* do projeto de função e copie o valor da cadeia de ligação. Numa janela de terminais ou comando, execute o seguinte comando para criar uma variável ambiental chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a sua cadeia de ligação específica no lugar de `<connection_string>`. (Esta variável ambiental significa que não precisa de fornecer a cadeia de ligação a cada comando subsequente utilizando o argumento `--connection-string`.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Opcional) Utilize o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para visualizar as filas de armazenamento na sua conta. A saída deste comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função escreveu a sua primeira mensagem para aquela fila.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Utilize o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para visualizar as mensagens nesta fila, que deve ser o primeiro nome utilizado ao testar a função anteriormente. O comando recupera a primeira mensagem na fila na [codificação base64,](functions-bindings-storage-queue.md#encoding)pelo que também deve descodificar a mensagem para visualizar como texto.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Porque precisa de desreferenciar a recolha de mensagens e descodificar a partir do base64, executar powerShell e utilizar o comando PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Reutilizar o projeto para o Azure

Agora que testou a função localmente e verificou que escreveu uma mensagem para a fila de armazenamento do Azure, pode reimplantar o seu projeto para atualizar o ponto final em execução no Azure.

1. Na pasta *LocalFunctionsProj,* utilize o comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) para reimplantar o projeto, substituindo`<app_name>` pelo nome da sua aplicação.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Tal como no quickstart anterior, utilize um navegador ou CURL para testar a função reimplantada.

    # <a name="browsertabbrowser"></a>[Browser](#tab/browser)
    
    Copie o **url invocado** completo mostrado na saída do comando de publicação numa barra de endereços de navegador, adiando o parâmetro de consulta `&name=Azure`. O navegador deve apresentar uma saída semelhante à que executou a função localmente.

    ![A saída da função é executada no Azure num browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[curl](#tab/curl)
    
    Corra [o caracol](https://curl.haxx.se/) com a **url Invoca,** aparando o parâmetro `&name=Azure`. A saída do comando deve ser o texto, "Olá Azure".
    
    ![A saída da função funciona em Azure usando caracóis](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Examine novamente a fila de armazenamento, conforme descrito na secção anterior, para verificar se contém a nova mensagem escrita na fila.

    Se estiver a utilizar o Azure CLI para examinar a fila, o comando `az storage message peek` mostra apenas a primeira mensagem na fila. Para simular o processamento das mensagens, use `az storage message get` em vez disso com todos os mesmos argumentos. O comando `get` devolve a mensagem e retira-a da fila. Pode então repetir o mesmo comando até que a fila esteja vazia (e o comando dá um erro).

## <a name="clean-up-resources"></a>Limpar recursos

Se continuar até ao próximo passo, [Enable Application Insights integração,](functions-monitoring.md#manually-connect-an-app-insights-resource)mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar a integração de Insights de Aplicação com uma aplicação De Funções Azure](functions-monitoring.md#manually-connect-an-app-insights-resource)

Outros recursos:

- [Exemplos de projetos de função completa em Python.](/samples/browse/?products=azure-functions&languages=python)
- [Guia de desenvolvimento de funções azure Python](functions-reference-python.md)
- [Funções Azure aciona matoe e encadernações.](functions-triggers-bindings.md)
- [Página de preços de funções](https://azure.microsoft.com/pricing/details/functions/)
- [Estimativa do plano de consumo custa](functions-consumption-costs.md) artigo.
