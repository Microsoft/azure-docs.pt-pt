---
title: Criar uma função Python disparada por HTTP no Azure
description: Crie e implemente o código Python sem servidor estoirar para a nuvem utilizando funções Azure.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 01c586c9077fd8cf244d7e26fe55252cc455c6fb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710955"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Início rápido: criar uma função Python disparada por HTTP no Azure

Neste artigo, utiliza ferramentas de linha de comando para criar uma função Python que responda aos pedidos http. Depois de testar o código localmente, implementa-o para o ambiente sem servidor estoirar das Funções Azure. Completar este quickstart incorre num pequeno custo de alguns cêntimos de USD ou menos na sua conta Azure.

Também há uma [versão baseada em Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01) deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- O [Azure Funs Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou um posterior.
- A versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 ou posterior. 
- [Python 3.7.4 - 64 bits.](https://www.python.org/downloads/release/python-374/) (Python 3.7.4 é verificado com funções Azure; As versões Python 3.8 e posteriores ainda não são suportadas.)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminais ou comando, faça `func --version` para verificar se as Ferramentas Core funções Do Azure são a versão 2.7.1846 ou posterior.
1. Execute `az --version` para verificar se a versão Azure CLI é de 2.0.76 ou mais tarde.
1. Execute `az login` para iniciar sessão no Azure e verificar uma subscrição ativa.
1. Execute `python --version` (Linux/MacOS) ou `py --version` (Windows) para verificar os relatórios da versão Python 3.7.x.

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Numa pasta adequada, execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`. Certifique-se de utilizar python 3.7, que é suportado por Funções Azure.


# <a name="bashtabbash"></a>[bash](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Executa todos os comandos subsequentes neste ambiente virtual ativado. (Para sair do ambiente virtual, corra `deactivate`.)

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

Nas Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função.

1. No ambiente virtual, execute o comando `func init` para criar um projeto de funções numa pasta chamada *LocalFunctionProj* com o tempo de execução especificado:

    ```
    func init LocalFunctionProj --python
    ```
    
    Esta pasta contém vários ficheiros para o projeto, incluindo ficheiros de configurações chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *as configurações locais.json* podem conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

    > [!TIP]
    > Como um projeto de função está ligado a um tempo de execução específico, todas as funções do projeto devem ser escritas com a mesma língua.

1. Navegue na pasta do projeto:

    ```
    cd LocalFunctionProj
    ```
    
1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o argumento `--name` é o nome único da sua função e o `--template` argumento especifica o gatilho da função. `func new` criar uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração chamado *função.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do ficheiro

Se desejar, pode saltar para [executar a função localmente](#run-the-function-locally) e examinar o conteúdo do ficheiro mais tarde.

### <a name="__init__py"></a>\_\_init e\_\_.py

*\_\_init e\_\_ .py* contém uma função `main()` Python que é desencadeada de acordo com a configuração em *função.json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Para o gatilho HTTP, a função recebe dados de pedido na variável `req` conforme definido em *função.json*. `req` é um exemplo da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *Function. JSON*, é uma instância da [classe Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, consulte [Azure Functions gatilhos e associações http](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*function.json* é um ficheiro de configuração que define a entrada e a saída `bindings` para a função, incluindo o tipo de gatilho. Pode alterar `scriptFile` invocar um ficheiro Python diferente, se desejar.

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

Cada encadernação requer uma direção, um tipo e um nome único. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e a associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Executar localmente a função

Inicie a função iniciando o hospedeiro de funcionamento das Funções Azure locais na pasta *LocalFunctionProj:*

```
func start
```

A seguinte saída deve aparecer. (Se o HttpExample não aparecer como mostrado abaixo, provavelmente iniciou o hospedeiro a partir da pasta *HttpExample.* Nesse caso, utilize **o Ctrl**+**C** para parar o hospedeiro, navegar para a pasta *LocalFunctionProj* e executá`func start` novamente.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Copie o URL da sua função `HttpExample` a partir desta saída para um browser e ateste a `?name=<your-name>`de cadeia seletiva, tornando o URL completo como `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve apresentar uma mensagem como `Hello Functions`:

![Resultado da função executada localmente no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

O terminal em que correu `func start` também mostra saída de log à medida que faz pedidos.

Quando estiver pronto, **ctrl**+**C** para parar o hospedeiro das funções.

## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de apoio para a sua função

Para implementar o seu código de função para o Azure, precisa de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento Azure, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de funções Azure, que fornece o ambiente para executar o seu código de função. Uma aplicação de função mapeia o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Usa comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

1. Se ainda não o fez, inicie sessão no Azure com o comando [de login az:](/cli/azure/group#az-login)

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`. (Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível a partir do comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Você não pode hospedar aplicativos Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente chamado `AzureFunctionsQuickstart-rg` com uma aplicação de função Windows ou aplicação web, deve utilizar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento geral no seu grupo de recursos e região utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, substitua `<storage_name>` por um nome globalmente único adequado a si. Os nomes devem conter três a 24 caracteres e apenas letras minúsculas. `Standard_LRS` especifica uma conta típica de uso geral.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento incorre apenas alguns cêntimos de USD para este arranque rápido.
    
1. Criar a aplicação Funções utilizando o [app de funções az criar](/cli/azure/functionapp#az-functionapp-create) comando. No exemplo seguinte, substitua `<storage_name>` pelo nome da conta que usou no passo anterior e substitua `<app_name>` por um nome globalmente único adequado a si. O `<app_name>` também é o domínio DNS predefinido para a aplicação de funções.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Este comando cria uma aplicação de função que executa o tempo de funcionamento do idioma especificado no âmbito do Plano de Consumo de [Funções Azure,](functions-scale.md#consumption-plan)que é gratuito para a quantidade de utilização que incorra aqui. O comando também prevê uma instância de Insights de Aplicação Azure associada no mesmo grupo de recursos, com o qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [monitorar Azure Functions](functions-monitoring.md). A instância não incorre em custos até que o ative.
    
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para o Azure

Com os recursos necessários, está agora pronto para implementar o seu projeto de funções locais para a aplicação de funções em Azure, utilizando o comando [de publicação func azure functionapp.](functions-run-local.md#project-file-deployment) No exemplo seguinte, substitua `<app_name>` pelo nome da sua aplicação.

```
func azure functionapp publish <app_name>
```

Se vir o erro, "Não consigo encontrar a app com nome...", espere alguns segundos e tente novamente, já que o Azure pode não ter inicializado totalmente a app após o comando de `az functionapp create` anterior.

O comando de publicação apresenta resultados semelhantes aos seguintes resultados (truncados para a simplicidade):

```output
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
```

## <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido http para o seu URL no navegador ou com uma ferramenta como o curl. Em ambos os casos, o `code` parâmetro URL é a sua chave de função única que autoriza a invocação com o ponto final da sua função.

# <a name="browsertabbrowser"></a>[Browser](#tab/browser)

Copie o **url invocado** completo mostrado na saída do comando de publicação numa barra de endereços de navegador, adiando o parâmetro de consulta `&name=Azure`. O navegador deve apresentar uma saída semelhante à que executou a função localmente.

![A saída da função é executada no Azure num browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Corra [o caracol](https://curl.haxx.se/) com a **url Invoca,** aparando o parâmetro `&name=Azure`. A saída do comando deve ser o texto, "Olá Azure".

![A saída da função funciona em Azure usando caracóis](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Para exibir logs quase em tempo real para um aplicativo Python publicado, use o [Live Metrics Stream Application insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Limpar recursos

Se continuar até ao próximo passo, Adicione uma ligação de saída de fila de [armazenamento Azure,](functions-add-output-binding-storage-queue-python.md)mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma ligação de saída de fila de armazenamento Azure](functions-add-output-binding-storage-queue-python.md)
