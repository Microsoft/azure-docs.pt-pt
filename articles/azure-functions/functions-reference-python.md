---
title: Referência do desenvolvedor do Python para Azure Functions
description: Entenda como desenvolver funções com Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 0cdd7f291b43f442b8471a19f515e4a2d12b4e74
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562871"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor do Azure Functions Python

Este artigo é uma introdução ao desenvolvimento de Azure Functions usando o Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia do Azure Functions desenvolvedores](functions-reference.md).

## <a name="programming-model"></a>Modelo de programação

Uma função do Azure deve ser um método sem estado em seu script Python que processa a entrada e produz a saída. Por padrão, o tempo de execução espera que o método seja implementado como um método `main()` global chamado `__init__.py` no arquivo.

Você pode alterar a configuração padrão especificando as `scriptFile` Propriedades e `entryPoint` no arquivo *Function. JSON* . Por exemplo, a _função. JSON_ a seguir informa o tempo de execução `customentry()` para usar o método no arquivo _Main.py_ , como o ponto de entrada para sua função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Os dados de gatilhos e associações são associados à função por meio de atributos de `name` método usando a propriedade definida no arquivo *Function. JSON* . Por exemplo, a _Function. JSON_ abaixo descreve uma função simples disparada por uma solicitação `req`http denominada:

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

Opcionalmente, para aproveitar o IntelliSense e os recursos de preenchimento automático fornecidos pelo editor de código, você também pode declarar os tipos de atributo e tipo de retorno na função usando anotações do tipo Python. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [Azure. Functions. *](/python/api/azure-functions/azure.functions?view=azure-python) para associar entradas e saídas aos seus métodos.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas para um projeto de funções do Python é semelhante ao seguinte:

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

Há um arquivo [host. JSON](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem seu próprio arquivo de código e arquivo de configuração de associação (Function. JSON). 

O código compartilhado deve ser mantido em uma pasta separada. Para fazer referência a módulos na pasta SharedCode, você pode usar a seguinte sintaxe:

```
from __app__.SharedCode import myFirstHelperFunction
```

Ao implantar um projeto de função em seu aplicativo de funções no Azure, todo o conteúdo da pasta *FunctionApp* deve ser incluído no pacote, mas não na própria pasta.

## <a name="triggers-and-inputs"></a>Gatilhos e entradas

As entradas são divididas em duas categorias no Azure Functions: disparar entrada e entrada adicional. Embora sejam diferentes no arquivo, `function.json` o uso é idêntico no código Python.  Cadeias de conexão ou segredos para fontes de entrada e gatilho são mapeados para valores no `local.settings.json` arquivo ao serem executados localmente e as configurações do aplicativo durante a execução no Azure. 

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

Quando a função é invocada, a solicitação HTTP é passada para a função `req`como. Uma entrada será recuperada do armazenamento de BLOBs do Azure com base na _ID_ na URL de rota e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de `AzureWebJobsStorage` conexão encontrada em que é a mesma conta de armazenamento usada pelo aplicativo de funções.


## <a name="outputs"></a>outputs

A saída pode ser expressa no valor de retorno e nos parâmetros de saída. Se houver apenas uma saída, recomendamos o uso do valor de retorno. Para várias saídas, você terá que usar parâmetros de saída.

Para usar o valor de retorno de uma função como o valor de uma associação de saída `name` , a propriedade da associação deve ser definida `$return` como `function.json`in.

Para produzir várias saídas, use o `set()` método fornecido [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) pela interface para atribuir um valor à associação. Por exemplo, a função a seguir pode enviar uma mensagem por push a uma fila e também retornar uma resposta HTTP.

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

O acesso ao agente de tempo de execução do Azure Functions está [`logging`](https://docs.python.org/3/library/logging.html#module-logging) disponível por meio de um manipulador raiz em seu aplicativo de funções. Esse agente está vinculado a Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir registra uma mensagem de informação quando a função é invocada por meio de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Há métodos de log adicionais disponíveis que permitem que você grave no console em diferentes níveis de rastreamento:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| logout. **crítico (_mensagem_)**   | Grava uma mensagem com nível crítico no agente raiz.  |
| logout. **erro (_mensagem_)**   | Grava uma mensagem com um erro de nível no agente raiz.    |
| logout. **aviso (_mensagem_)**    | Grava uma mensagem com aviso de nível no agente raiz.  |
| logout. **informações (_mensagem_)**    | Grava uma mensagem com informações de nível no agente raiz.  |
| logout. **depurar (_mensagem_)** | Grava uma mensagem com depuração de nível no agente raiz.  |

## <a name="async"></a>Async

Recomendamos que você escreva sua função do Azure como uma corrotina assíncrona `async def` usando a instrução.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Se a função main () for síncrona (sem `async` qualificador), executaremos automaticamente a função `asyncio` em um pool de threads.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) inclua o argumento em sua assinatura. 

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe de [**contexto**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes métodos:

`function_directory`  
O diretório no qual a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não há garantia de que o estado do seu aplicativo será preservado para execuções futuras. No entanto, o tempo de execução de Azure Functions geralmente reutiliza o mesmo processo para várias execuções do mesmo aplicativo. Para armazenar em cache os resultados de uma computação cara, declare-o como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Versão do Python e gerenciamento de pacotes

Atualmente, Azure Functions dá suporte apenas a Python 3.6. x (distribuição oficial do CPython).

Ao desenvolver localmente usando o Azure Functions Core Tools ou Visual Studio Code, adicione os nomes e as versões dos pacotes necessários ao `requirements.txt` arquivo e instale-os usando `pip`o.

Por exemplo, o arquivo de requisitos e o comando Pip a seguir podem ser usados `requests` para instalar o pacote do PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicando no Azure

Quando você estiver pronto para publicar, verifique se todas as suas dependências estão listadas no arquivo *requirements. txt* , que está localizado na raiz do diretório do projeto. Se você estiver usando um pacote que requer um compilador e não oferecer suporte à instalação de rodas compatíveis com manylinux do PyPI, a publicação no Azure falhará com o seguinte erro: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Para criar e configurar automaticamente os binários necessários, [Instale](https://docs.docker.com/install/) o Docker no computador local e execute o comando a seguir para publicar usando o [Azure Functions Core Tools](functions-run-local.md#v2) (Func). Lembre-se `<app name>` de substituir pelo nome do seu aplicativo de funções no Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Nos bastidores, as ferramentas principais usarão o Docker para executar a imagem [MCR.Microsoft.com/Azure-Functions/Python](https://hub.docker.com/r/microsoft/azure-functions/) como um contêiner em seu computador local. Usando esse ambiente, ele criará e instalará os módulos necessários da distribuição de origem, antes de compactá-los para implantação final no Azure.

Para criar suas dependências e publicar usando um sistema de entrega contínua (CD), [use pipelines do Azure DevOps](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Testes de unidade

As funções escritas em Python podem ser testadas como outros códigos Python usando estruturas de teste padrão. Para a maioria das associações, é possível criar um objeto de entrada fictício criando uma instância de uma classe apropriada do `azure.functions` pacote. Como o [`azure.functions`](https://pypi.org/project/azure-functions/) pacote não está disponível imediatamente, certifique-se de instalá-lo `requirements.txt` por meio do arquivo, conforme descrito na seção [versão e gerenciamento de pacotes do Python](#python-version-and-package-management) acima.

Por exemplo, a seguir está um teste fictício de uma função disparada por HTTP:

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

Aqui está outro exemplo, com uma função disparada por fila:

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

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos e as solicitações de recursos são rastreados usando a lista de [problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues) . Se você tiver um problema e não conseguir encontrar o problema no GitHub, abra um novo problema e inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Documentação da API do pacote Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)
* [Associações de armazenamento de BLOBs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de armazenamento de filas](functions-bindings-storage-queue.md)
* [Acionador de temporizador](functions-bindings-timer.md)
