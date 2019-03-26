---
title: Referência para programadores do Python para as funções do Azure
description: Aprenda a desenvolver as funções com Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure funções, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 28f2b395c7f9be1b194b500ef20456be8ff405b0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438702"
---
# <a name="azure-functions-python-developer-guide"></a>Guia de programadores de Python de funções do Azure

Este artigo é uma introdução ao desenvolvimento das funções do Azure com o Python. O conteúdo abaixo pressupõe que já leu a [guia de programadores do funções do Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modelo de programação

Uma função do Azure deve ser um método sem monitoração de estado no seu script de Python que processa as entradas e produz um resultado. Por predefinição, o tempo de execução espera esta opção para ser implementado como um método global chamado `main()` no `__init__.py` ficheiro.

Pode alterar a configuração predefinida, especificando o `scriptFile` e `entryPoint` propriedades no `function.json` ficheiro. Por exemplo, o _Function_ abaixo indica o tempo de execução para utilizar o _customentry()_ método no _main.py_ ficheiro, como o ponto de entrada para a função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Dados a partir de acionadores e enlaces estão vinculados à função por meio dos atributos do método a utilizar o `name` propriedade definida no `function.json` ficheiro de configuração. Por exemplo, o _Function_ abaixo descreve uma função simples acionada por um pedido HTTP com o nome `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

O `__init__.py` arquivo contém o seguinte código de função:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Opcionalmente, também pode declarar os tipos de parâmetro e tipo de retorno na função anotações de tipo de Python a utilizar. Por exemplo, a mesma função pode ser escrita usando anotações, da seguinte forma:

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Utilize as anotações de Python incluídas nos [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) pacote para o enlace de entrada e saídas para seus métodos. 

## <a name="folder-structure"></a>estrutura de pastas

A estrutura de pastas para um projeto de funções de Python é semelhante ao seguinte:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

Existe um partilhada [Host. JSON](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem seu próprio arquivo de código e o ficheiro de configuração de vinculação (Function). 

Código compartilhado deve ser mantido numa pasta separada. Para referenciar os módulos na pasta SharedCode, pode usar a seguinte sintaxe:

```
from ..SharedCode import myFirstHelperFunction
```

Extensões de ligação utilizadas pelo tempo de execução de funções são definidas no `extensions.csproj` arquivo, com os ficheiros de biblioteca real no `bin` pasta. Ao desenvolver localmente, deve [registar as extensões de vinculação](./functions-bindings-register.md#local-development-azure-functions-core-tools) com ferramentas de núcleo de funções do Azure. 

Quando implementar um projeto de funções para a aplicação de funções no Azure, todo o conteúdo da pasta FunctionApp deve ser incluído no pacote, mas não a pasta em si.

## <a name="inputs"></a>Entradas

Entradas são divididas em duas categorias nas funções do Azure: a entrada de Acionador e entrada adicional. Embora eles sejam diferentes em `function.json`, a utilização é idêntica no código de Python. Vejamos o seguinte trecho de código como exemplo:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, o pedido HTTP é passado para a função como `req`. Uma entrada será recuperada de armazenamento de Blobs do Azure com base na _id_ o URL de rota e disponibilizados tal como `obj` no corpo da função.

## <a name="outputs"></a>Saídas

Saída pode ser expressas no valor de retorno e parâmetros de saída. Se existir apenas uma saída, recomendamos que utilize o valor de retorno. Para várias saídas, terá que usar parâmetros de saída.

Para utilizar o valor de retorno de uma função como o valor de um enlace de saída, o `name` propriedade da ligação deve ser definida como `$return` no `function.json`.

Para produzir várias saídas, utilize o `set()` método fornecido pelo `azure.functions.Out` interface para atribuir um valor para o enlace. Por exemplo, a seguinte função pode enviar uma mensagem para uma fila e também devolver uma resposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registo

Acesso ao agente de log de tempo de execução das funções do Azure está disponível por meio de uma raiz [ `logging` ](https://docs.python.org/3/library/logging.html#module-logging) manipulador na sua aplicação de função. Este agente de log está associado ao Application Insights e permite-lhe sinalizador avisos e erros encontrados durante a execução de função.

O exemplo seguinte regista uma mensagem de informações quando a função é invocada por meio de um acionador HTTP.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Métodos de registo adicionais estão disponíveis que permitem a escreve no console em diferentes níveis de rastreio:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| o registo. **críticas (_mensagem_)**   | Escreve uma mensagem com o nível crítico em agente a raiz.  |
| logging.**error(_message_)**   | Escreve uma mensagem com erro de nível no agente a raiz.    |
| o registo. **aviso (_mensagem_)**    | Escreve uma mensagem com o nível aviso no agente a raiz.  |
| logging.**info(_message_)**    | Escreve uma mensagem com o nível informações sobre o agente de log de raiz.  |
| logging.**debug(_message_)** | Escreve uma mensagem com depuração nível no agente a raiz.  |

## <a name="importing-shared-code-into-a-function-module"></a>Importar o código compartilhado num módulo de função

Módulos de Python publicados juntamente com módulos de função têm de ser importados com a sintaxe de importação relativo:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Em alternativa, colocar o código compartilhado num pacote autônomo, publicá-lo para um público ou uma instância de PyPI privada e especificá-la como uma dependência regular.

## <a name="async"></a>Async

Uma vez que apenas um único processo de Python pode existir por aplicação de funções, recomenda-se para implementar a sua função do Azure como um coroutine assíncrona utilizando o `async def` instrução.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Se a função Main () é síncrona (sem `async` qualificador) podemos executar automaticamente `asyncio` pool de threads.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o `context` argumento em sua assinatura. 

Por exemplo:

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

O **contexto** classe tem os seguintes métodos:

`function_directory`  
O diretório em que a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID de invocação da função atual.

## <a name="python-version-and-package-management"></a>Gerenciamento de versão e o pacote do Python

Atualmente, as funções do Azure só suporta a Python 3.6.x (oficial CPython distribuição).

Ao desenvolver localmente com as ferramentas de núcleo de funções do Azure ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para o `requirements.txt` de ficheiros e instalá-los usando `pip`.

Por exemplo, pode ser utilizado o seguinte comando de ficheiro e o pip de requisitos para instalar o `requests` pacote a partir da PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Quando estiver pronto para publicação, certifique-se de que todas as suas dependências estão listadas no `requirements.txt` arquivo, localizado na raiz do seu diretório do projeto. Para executar com êxito as suas funções do Azure, o ficheiro de requisitos deve conter um mínimo dos seguintes pacotes:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publicação no Azure

Se estiver a utilizar um pacote que necessita de um compilador e não suporta a instalação do manylinux compatível com rodas do PyPI, publicação para o Azure irá falhar com o seguinte erro: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Para criar e configurar os binários necessários, automaticamente [instalar o Docker](https://docs.docker.com/install/) no seu computador local e execute o seguinte comando para publicar utilizando o [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2) (func). Não se esqueça de substituir `<app name>` com o nome da sua aplicação de função no Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Em segundo plano, as ferramentas de núcleo irá utilizar o docker para executar o [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) imagem como um contentor no seu computador local. Utilizar este ambiente, ele vai, em seguida, criar e instalar os módulos necessários da distribuição de origem, antes de empacotá-los para a implantação final para o Azure.

> [!NOTE]
> Ferramentas de núcleo (func) utiliza o programa de PyInstaller para fixar o código do utilizador e as dependências num único executável autónomo para executar no Azure. Esta funcionalidade está atualmente em pré-visualização e não pode expandir a todos os tipos de pacotes de Python. Se não é possível importar os módulos, tente publicar novamente usando o `--no-bundler` opção. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Se continuar a ter problemas, faça contato pelo [abrindo um problema](https://github.com/Azure/azure-functions-core-tools/issues/new) e incluir uma descrição do problema. 


Para criar as suas dependências e publicar através de um sistema de entrega contínua (CD) e integração contínua (CI), pode utilizar um [Pipeline do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) ou [Travis CI de script personalizado](https://docs.travis-ci.com/user/deployment/script/). 

Segue-se um exemplo `azure-pipelines.yml` script para o processo de compilação e a publicação.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Segue-se um exemplo `.travis.yaml` script para o processo de compilação e a publicação.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Problemas conhecidos e FAQ

Todos os pedidos de funcionalidades e problemas conhecidos são controlados usando [problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues) lista. Se se deparar com um problema e não é possível localizar o problema no GitHub, abra um novo problema e incluir uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)
* [Enlaces de armazenamento de BLOBs](functions-bindings-storage-blob.md)
* [Enlaces de HTTP e Webhook](functions-bindings-http-webhook.md)
* [Enlaces de armazenamento de filas](functions-bindings-storage-queue.md)
* [Acionador de temporizador](functions-bindings-timer.md)
