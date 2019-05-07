---
title: Criar uma função acionada por HTTP no Azure
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
ms.openlocfilehash: 7e2b3424c3d8edc931054dea062280ea7789dc44
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143062"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Criar uma função acionada por HTTP no Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Este artigo mostra-lhe como utilizar ferramentas de linha de comandos para criar um projeto de Python que é executado nas funções do Azure. A função de que criar é acionada por pedidos de HTTP. Por fim, publicar o seu projeto para ser executado como um [função sem servidor](functions-scale.md#consumption-plan) no Azure.

Este artigo é o primeiro dos dois inícios rápidos das funções do Azure. Depois de concluir este artigo, [adicione o armazenamento do Azure enlace de saída da fila](functions-add-output-binding-storage-queue-python.md) à sua função.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tem de ter o seguinte:

+ Instale [Python 3.6](https://www.python.org/downloads/).

+ Instale [ferramentas de núcleo de funções do Azure](./functions-run-local.md#v2) versão 2.6.666 ou posterior.

+ Instalar o [CLI do Azure](/cli/azure/install-azure-cli) versão 2.x ou posterior.

+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Localmente, desenvolver e testar as funções de Python, tem de trabalhar num ambiente do Python 3.6. Execute os seguintes comandos para criar e ativar um ambiente virtual com o nome `.env`.

### <a name="bash-or-a-terminal-window"></a>Bash ou uma janela de terminal:

```bash
python3.6 -m venv .env
source .env/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou uma linha de comandos do Windows:

```powershell
py -3.6 -m venv .env
.env\scripts\activate
```

Os comandos restantes são executados dentro do ambiente virtual.

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um projeto de funções é o equivalente a uma aplicação de funções no Azure. Ele pode ter várias funções de que todas partilham as mesmas configurações de locais e de hospedagem.

No ambiente virtual, execute o seguinte comando, escolhendo **python** como seu tempo de execução do trabalho.

```command
func init MyFunctionProj
```

Uma pasta denominada _MyFunctionProj_ é criada, que contém os seguintes três arquivos:

* `local.settings.json` é utilizado para armazenar definições de aplicações e as cadeias de ligação ao executar localmente. Não publicar este ficheiro para o Azure.
* `requirements.txt` contém a lista de pacotes a serem instalados na publicação no Azure.
* `host.json` contém opções de configuração globais que afetam todas as funções na aplicação de funções. Publicar este ficheiro para o Azure.

Navegue para a nova pasta de MyFunctionProj:

```command
cd MyFunctionProj
```

Em seguida, atualizar o ficheiro de Host. JSON para permitir que os pacotes de extensão.  

## <a name="reference-bindings"></a>Enlaces de referência

Os pacotes de extensão torna mais fácil de adicionar extensões de vinculação mais adiante. Também elimina a necessidade de instalar o .NET Core 2.x SDK. Os pacotes de extensão requer a versão 2.6.1071 das ferramentas de núcleo ou uma versão posterior. 

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora, pode adicionar uma função ao seu projeto.

## <a name="create-a-function"></a>Criar uma função

Para adicionar uma função ao seu projeto, execute o seguinte comando:

```command
func new
```

Escolha o **acionador HTTP** modelo, o tipo `HttpTrigger` como o nome da função, em seguida, prima Enter.

Uma subpasta com o nome _HttpTrigger_ é criada, que contém os seguintes ficheiros:

* **Function**: ficheiro de configuração que define a função, acionador e outras ligações. Consulte este ficheiro e ver que o valor para `scriptFile` aponta para o ficheiro que contém a função, enquanto o acionador de invocação e enlaces são definidos no `bindings` matriz.

  Cada ligação requer uma direção, tipo e um nome exclusivo. O acionador HTTP tem um enlace de entrada do tipo [ `httpTrigger` ](functions-bindings-http-webhook.md#trigger) e o enlace do tipo de saída [ `http` ](functions-bindings-http-webhook.md#output).

* **__Init__. PY**: função acionada por um ficheiro de script que é o HTTP. Este script de rever e ver que ele contém um padrão `main()`. Dados HTTP do acionador são passados para esta função com o `req` com o parâmetro de enlace de nome. Definido na Function, `req` é uma instância do [azure.functions.HttpRequest classe](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido como `$return` Function, é uma instância do [azure.functions.HttpResponse classe](/python/api/azure-functions/azure.functions.httpresponse). Para obter mais informações, consulte [HTTP de funções do Azure acionadores e enlaces](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Executar localmente a função

O comando seguinte inicia a aplicação de função, o que é executada localmente utilizando o mesmo runtime das funções do Azure que está no Azure.

```bash
func host start
```

Quando o anfitrião de funções é iniciado, ele escreve algo como o resultado seguinte, que foi truncado para melhorar a legibilidade:

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

## <a name="create-a-function-app-in-azure"></a>Criar uma aplicação de funções no Azure

Uma aplicação de funções fornece um ambiente para executar o código de função. Permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Execute o seguinte comando utilizando um nome de aplicação de funções exclusivo em vez do `<APP_NAME>` marcador de posição e a conta de armazenamento de nomes para `<STORAGE_NAME>`. O `<APP_NAME>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Não não possível alojar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente denominado `myResourceGroup` com uma aplicação de funções do Windows ou a aplicação web, tem de utilizar um grupo de recursos diferente.

Agora, está pronto para publicar o projeto de funções de local para a aplicação de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Passos Seguintes

Criou um projeto de funções de Python com uma função acionada por HTTP, executá-lo no seu computador local e implementou-a para o Azure. Agora, expanda a sua função por...

> [!div class="nextstepaction"]
> [Armazenamento do Azure a adicionar fila de enlace de saída](functions-add-output-binding-storage-queue-python.md)
