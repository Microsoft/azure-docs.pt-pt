---
title: Criar a sua primeira função de Python no Azure
description: Saiba como criar sua primeira função de Python no Azure com as ferramentas de núcleo de funções do Azure e a CLI do Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: af684a4fcc3a70326c1a57cb10a39204b4fd12dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438755"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Criar a sua primeira função de Python no Azure (pré-visualização)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Este artigo de início rápido explica-lhe como utilizar a CLI do Azure para criar a sua primeira [sem servidor](https://azure.com/serverless) aplicação em execução no Linux de função de Python. O código de função é criado localmente e, em seguida, implementado no Azure através das [Azure Functions Core Tools](functions-run-local.md). Para saber mais sobre as considerações de pré-visualização para executar as suas aplicações de função no Linux, veja [funciona no artigo de Linux](https://aka.ms/funclinux).

São suportados os seguintes passos num computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e testar localmente, precisará para:

+ Instale [Python 3.6](https://www.python.org/downloads/).

+ Instale [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2) versão 2.2.70 ou posterior (requer o .NET Core 2.x SDK).

Para publicar e executar no Azure:

+ Instalar o [CLI do Azure]( /cli/azure/install-azure-cli) versão 2.x ou posterior.

+ Precisa de uma subscrição do Azure Active Directory.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Para criar um projeto de funções, é necessário que trabalha num ambiente virtual do Python 3.6. Execute os seguintes comandos para criar e ativar um ambiente virtual com o nome `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Agora, pode criar um projeto de funções local. Este diretório é o equivalente a uma aplicação de funções no Azure. Pode conter várias funções que partilham a mesma configuração de local e de hospedagem.

Na janela de terminal ou a partir de um prompt de comando, execute o seguinte comando:

```bash
func init MyFunctionProj
```

Escolher **python** como o tempo de execução desejado.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Verá algo parecido com o resultado seguinte.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Uma nova pasta chamada _MyFunctionProj_ é criado. Para continuar, altere o diretório para esta pasta.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```bash
func new
```

Escolher `HTTP Trigger` como o modelo e forneça um **nome da função** de `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Verá algo parecido com o resultado seguinte.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Uma subpasta nomeada _HttpTrigger_ é criado. Ele contém `__init__.py` que é o arquivo de script principal e `function.json` ficheiro que descreve o acionador e as ligações usadas pela função. Para saber mais sobre o modelo de programação, pode consultar o [guia de programadores de Python de funções do Azure](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Executar localmente a função

Utilize o seguinte comando para executar o host de funções localmente.

```bash
func host start
```

Quando o anfitrião de funções é iniciado, ele produz o URL da sua função acionada por HTTP. (Observe que a saída completa foi truncada para facilitar a leitura.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Copie o URL da sua função a partir da saída e cole-a na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

Captura de ecrã seguinte mostra a resposta da função, quando é acionada a partir do browser:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Agora está pronto para criar uma aplicação de funções e outros recursos necessários para publicação no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Criar uma aplicação de funções do Linux no Azure

A aplicação de funções fornece um ambiente para executar o código de função. Permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos. Criar uma **em execução no Linux de aplicação de função de Python** utilizando o [az functionapp criar](/cli/azure/functionapp) comando.

Execute o seguinte comando utilizando um nome de aplicação de funções exclusivo em vez do `<app_name>` marcador de posição e a conta de armazenamento de nomes para `<storage_name>`. O `<app_name>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Se tiver um grupo de recursos existente denominado `myResourceGroup` com todas as aplicações que não são do Serviço de Aplicações, tem de utilizar um grupo de recursos diferente. Não pode alojar aplicações Windows e Linux no mesmo grupo de recursos.  

Depois da aplicação de função tiver sido criada, verá a seguinte mensagem:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Está agora pronto para publicar o projeto de funções de local para a aplicação de funções no Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementar o projeto da aplicação de funções no Azure

Utilizar as ferramentas de núcleo de funções do Azure, execute o seguinte comando. Substitua `<app_name>` com o nome da sua aplicação do passo anterior.

```bash
func azure functionapp publish <app_name>
```

Verá algo parecido com o resultado seguinte, que foi truncado para facilitar a leitura.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o desenvolvimento das funções do Azure com o Python.

> [!div class="nextstepaction"]
> [Guia de programadores de Python de funções do Azure](functions-reference-python.md)
> [as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
