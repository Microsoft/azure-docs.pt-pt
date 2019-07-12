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
ms.openlocfilehash: 14594e95efe94fe38502dc6269627158c42a04be
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622358"
---
# <a name="azure-functions-python-developer-guide"></a>Guia de programadores de Python de funções do Azure

Este artigo é uma introdução ao desenvolvimento das funções do Azure com o Python. O conteúdo abaixo pressupõe que já leu a [guia de programadores do funções do Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modelo de programação

Uma função do Azure deve ser um método sem monitoração de estado no seu script de Python que processa as entradas e produz um resultado. Por predefinição, o tempo de execução espera que o método a ser implementado como um método global chamado `main()` no `__init__.py` ficheiro.

Pode alterar a configuração predefinida, especificando o `scriptFile` e `entryPoint` propriedades no *Function* ficheiro. Por exemplo, o _Function_ abaixo indica o tempo de execução para utilizar o `customentry()` método na _main.py_ ficheiro, como o ponto de entrada para a função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Dados a partir de acionadores e enlaces estão vinculados à função por meio dos atributos do método a utilizar o `name` propriedade definida no *Function* ficheiro. Por exemplo, o _Function_ abaixo descreve uma função simples acionada por um pedido HTTP com o nome `req`:

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

Opcionalmente, para aproveitar o intellisense e funcionalidades de conclusão automática de mensagens em fila fornecidas pelo seu editor de código, também pode declarar os tipos de atributo e tipo de retorno na função anotações de tipo de Python a utilizar. 

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
```

Existe um partilhada [Host. JSON](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem seu próprio arquivo de código e o ficheiro de configuração de vinculação (Function). 

Código compartilhado deve ser mantido numa pasta separada. Para referenciar os módulos na pasta SharedCode, pode usar a seguinte sintaxe:

```
from __app__.SharedCode import myFirstHelperFunction
```

Quando implementar um projeto de função para a aplicação de funções no Azure, todo o conteúdo do *FunctionApp* pasta deve ser incluída no pacote, mas não a pasta em si.

## <a name="triggers-and-inputs"></a>Acionadores e as entradas

Entradas são divididas em duas categorias nas funções do Azure: a entrada de Acionador e entrada adicional. Embora eles sejam diferentes no `function.json` ficheiro, a utilização é idêntica no código de Python.  Cadeias de ligação ou segredos para origens de entrada e de Acionador de mapeiam a valores no `local.settings.json` ficheiro ao executar localmente e as definições da aplicação quando em execução no Azure. 

Por exemplo, o código a seguir demonstra a diferença entre os dois:

```json
// function.json
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

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, o pedido HTTP é passado para a função como `req`. Uma entrada será recuperada de armazenamento de Blobs do Azure com base na _ID_ o URL de rota e disponibilizados tal como `obj` no corpo da função.  Aqui a conta de armazenamento especificado a cadeia de ligação se encontra no `AzureWebJobsStorage` que é a mesma conta de armazenamento utilizada pela aplicação de função.


## <a name="outputs"></a>outputs

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

## <a name="async"></a>Async

Recomendamos que escreve sua função do Azure como um coroutine assíncrona utilizando o `async def` instrução.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Se a função Main () é síncrona (sem `async` qualificador) executamos automaticamente a função `asyncio` pool de threads.

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

## <a name="global-variables"></a>Variáveis globais

Não é garantido que o estado da sua aplicação será mantido para execuções futuras. No entanto, o runtime das funções do Azure, muitas vezes, reutiliza o mesmo processo para várias execuções da mesma aplicação. Para colocar em cache os resultados de um cálculo complexo, declará-lo como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Gerenciamento de versão e o pacote do Python

Atualmente, as funções do Azure só suporta a Python 3.6.x (oficial CPython distribuição).

Ao desenvolver localmente com as ferramentas de núcleo de funções do Azure ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para o `requirements.txt` de ficheiros e instalá-los usando `pip`.

Por exemplo, pode ser utilizado o seguinte comando de ficheiro e o pip de requisitos para instalar o `requests` pacote a partir da PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicação no Azure

Quando estiver pronto para publicar, certifique-se de que todas as suas dependências estão listadas na *Requirements. txt* arquivo, o que está localizado na raiz do seu diretório do projeto. Se estiver a utilizar um pacote que necessita de um compilador e não suporta a instalação do manylinux compatível com rodas do PyPI, publicação para o Azure irá falhar com o seguinte erro: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Para criar e configurar os binários necessários, automaticamente [instalar o Docker](https://docs.docker.com/install/) no seu computador local e execute o seguinte comando para publicar utilizando o [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2) (func). Não se esqueça de substituir `<app name>` com o nome da sua aplicação de função no Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Em segundo plano, as ferramentas de núcleo irá utilizar o docker para executar o [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) imagem como um contentor no seu computador local. Utilizar este ambiente, ele vai, em seguida, criar e instalar os módulos necessários da distribuição de origem, antes de empacotá-los para a implantação final para o Azure.

Para criar as suas dependências e publicar a utilizar um sistema de entrega contínua (CD), [usar Pipelines do Azure DevOps](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Teste de unidade

As funções escritas no Python podem ser testadas como outro código de Python com estruturas de teste padrão. Para a maioria das ligações, é possível criar um objeto fictício de entrada através da criação de uma instância de uma classe adequada do `azure.functions` pacote. Uma vez que o [ `azure.functions` ](https://pypi.org/project/azure-functions/) pacote não está imediatamente disponível, certifique-se de que instalá-lo por meio de sua `requirements.txt` de ficheiros, tal como descrito na [gerenciamento de versão e o pacote do Python](#python-version-and-package-management) secção acima.

Por exemplo, segue-se um teste de simulação de uma função acionada por HTTP:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
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

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Eis outro exemplo, com uma função acionada por fila:

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
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
