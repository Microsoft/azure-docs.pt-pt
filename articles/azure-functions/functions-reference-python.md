---
title: Referência do desenvolvedor python para funções Azure
description: Entenda como desenvolver funções com Python
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: f9b81a7263dc9a1bdae9fd881519ac734da2c6bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642202"
---
# <a name="azure-functions-python-developer-guide"></a>Guia de desenvolvedores de Azure Functions Python

Este artigo é uma introdução ao desenvolvimento de Funções Azure usando Python. O conteúdo abaixo pressupõe que já leu o guia de desenvolvedores de [Funções Azure.](functions-reference.md)

Para projetos de amostra de função autónoma em Python, consulte as [amostras de Funções Python.](/samples/browse/?products=azure-functions&languages=python)

## <a name="programming-model"></a>Modelo de programação

A Azure Functions espera que uma função seja um método apátrida na sua escrita Python que processa a entrada e produz a saída. Por padrão, o tempo de execução espera que o método seja implementado como um método global chamado `main()` no `__init__.py` ficheiro. Também pode [especificar um ponto de entrada alternativo.](#alternate-entry-point)

Os dados de gatilhos e encadernações estão ligados à função através de atributos de método utilizando a `name` propriedade definida nofunction.js* no* ficheiro. Por exemplo, o  _function.jsabaixo_ descreve uma função simples desencadeada por um pedido HTTP denominado `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nesta definição, o `__init__.py` ficheiro que contém o código de função pode parecer o seguinte exemplo:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Também pode declarar explicitamente os tipos de atributos e o tipo de retorno na função utilizando anotações do tipo Python. Isto ajuda-o a utilizar as funcionalidades intellisense e autocompleta fornecidas por muitos editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilize as anotações Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para ligar as entradas e saídas aos seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Pode alterar o comportamento predefinido de uma função especificando opcionalmente as `scriptFile` propriedades e propriedades nofunction.js`entryPoint` * no* ficheiro. Por exemplo, o _function.jsabaixo_ indica o tempo de execução para utilizar o `customentry()` método no ficheiro _main.py,_ como ponto de entrada para a sua Função Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura de pasta recomendada para um projeto Python Functions parece o seguinte exemplo:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
A pasta principal do projeto \_ \_ \_ \_ (app) pode conter os seguintes ficheiros:

* *local.settings.jsem*: Usado para armazenar configurações de aplicações e cordas de conexão quando funciona localmente. Este ficheiro não é publicado no Azure. Para saber mais, consulte [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Contém a lista de pacotes que o sistema instala ao publicar no Azure.
* *host.jsem:* Contém opções de configuração global que afetam todas as funções numa aplicação de função. Este ficheiro é publicado no Azure. Nem todas as opções são suportadas quando se executa localmente. Para saber mais, veja [host.js.](functions-host-json.md)
* *.funcignore*: (Opcional) declara ficheiros que não devem ser publicados no Azure.
* *Dockerfile*: (Opcional) utilizado ao publicar o seu projeto num [recipiente personalizado](functions-create-function-linux-custom-image.md).

Cada função tem o seu próprio ficheiro de código e ficheiro de configuração de ligação (function.js).

Ao implementar o seu projeto numa aplicação de função em Azure, todo o conteúdo da pasta principal* \_ \_ (app) \_ \_ *deve ser incluído no pacote, mas não na própria pasta. Recomendamos que mantenha os seus testes numa pasta separada da pasta do projeto, neste exemplo `tests` . Isto impede-o de implementar código de teste com a sua aplicação. Para obter mais informações, consulte [o Teste de Unidade.](#unit-testing)

## <a name="import-behavior"></a>Comportamento de importação

Pode importar módulos no seu código de função utilizando referências relativas e absolutas explícitas. Com base na estrutura da pasta acima mostrada, as seguintes importações funcionam a partir da aplicação de ficheiro de função * \_ \_ \_ \_ \a minha \_ primeira \_ função \\ _ \_ init \_ \_ .py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

As seguintes importações *não funcionam* dentro do mesmo ficheiro:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

O código partilhado deve ser guardado numa pasta separada na * \_ \_ aplicação \_ \_ *. Para os módulos de referência na pasta de * \_ código partilhada,* pode utilizar a seguinte sintaxe:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Gatilhos e Entradas

As entradas dividem-se em duas categorias em Funções Azure: entrada de gatilho e entrada adicional. Embora sejam diferentes no ficheiro, o `function.json` uso é idêntico no código Python.  Cadeias de ligação ou segredos para gatilho e fontes de entrada mapeiam valores no `local.settings.json` ficheiro quando executam localmente, e as definições de aplicação ao executar em Azure.

Por exemplo, o seguinte código demonstra a diferença entre os dois:

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

Quando a função é invocada, o pedido HTTP é transmitido para a função como `req` . Uma entrada será recuperada a partir do Azure Blob Storage com base no _ID_ na rota URL e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de ligação encontrada na definição da aplicação AzureWebJobsStorage, que é a mesma conta de armazenamento utilizada pela aplicação de função.


## <a name="outputs"></a>Saídas

A saída pode ser expressa tanto em parâmetros de valor de retorno como de saída. Se houver apenas uma saída, recomendamos a utilização do valor de devolução. Para várias saídas, terá de utilizar parâmetros de saída.

Para utilizar o valor de retorno de uma função como valor de uma ligação de saída, a `name` propriedade da encadernação deve ser definida `$return` em `function.json` .

Para produzir várias saídas, utilize o `set()` método fornecido pela interface para atribuir um valor à [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) encadernação. Por exemplo, a seguinte função pode empurrar uma mensagem para uma fila e também devolver uma resposta HTTP.

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

O acesso ao runa de tempo de funcionamento do Azure Functions está disponível através de um manipulador de raiz [`logging`](https://docs.python.org/3/library/logging.html#module-logging) na sua aplicação de função. Este madeireiro está ligado a Insights de Aplicação e permite-lhe sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir regista uma mensagem de informação quando a função é invocada através de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Estão disponíveis métodos adicionais de registo que permitem escrever para a consola em diferentes níveis de vestígios:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Escreve uma mensagem com nível CRITICAL no madeir de raiz.  |
| **`error(_message_)`**   | Escreve uma mensagem com ERRO de nível no madeir de raiz.    |
| **`warning(_message_)`**    | Escreve uma mensagem com nível DE AVISO no madeir de raiz.  |
| **`info(_message_)`**    | Escreve uma mensagem com info nível no madeirão raiz.  |
| **`debug(_message_)`** | Escreve uma mensagem com depurrão de nível de DEBUG no madeirão de raiz.  |

Para saber mais sobre o registo, consulte [as Funções Monitor Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP Gatilho e encadernações

O gatilho HTTP é definido no function.jsficheiro. O `name` encadernação deve coincidir com o parâmetro indicado na função.
Nos exemplos anteriores, é utilizado um nome de encadernação. `req` Este parâmetro é um objeto [HttpRequest] e um objeto [HttpResponse] é devolvido.

A partir do objeto [HttpRequest,] pode obter cabeçalhos de pedido, parâmetros de consulta, parâmetros de rota e o corpo da mensagem.

O exemplo a seguir é do modelo de [gatilho HTTP para Python.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Nesta função, o valor do `name` parâmetro de consulta é obtido a partir do parâmetro do objeto `params` [HttpRequest.] O corpo de mensagem codificado json é lido usando o `get_json` método.

Da mesma forma, pode definir a `status_code` mensagem e `headers` para a mensagem de resposta no objeto [HttpResponse] devolvido.

## <a name="scaling-and-concurrency"></a>Escala e concuência

Por predefinição, o Azure Functions monitoriza automaticamente a carga da sua aplicação e cria casos adicionais de anfitrião para Python, conforme necessário. As funções utilizam limiares incorporados (não configuráveis pelo utilizador) para diferentes tipos de gatilhos para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para o QueueTrigger. Para mais informações, consulte [como funcionam os planos De Consumo e Premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Este comportamento de escala é suficiente para muitas aplicações. No entanto, as aplicações com uma das seguintes características não podem ser dimensionais tão eficazmente:

- A aplicação precisa de lidar com muitas invocações simultâneas.
- A aplicação processa um grande número de eventos de E/S.
- A aplicação está ligada à I/O.

Nesses casos, pode melhorar ainda mais o desempenho utilizando padrões de async e utilizando vários processos de trabalho linguístico.

### <a name="async"></a>Async

Como Python é um tempo de execução de um único fio, um exemplo de hospedeiro para Python pode processar apenas uma invocação de função de cada vez. Para aplicações que processam um grande número de eventos de E/S e/ou está ligado a I/O, você pode melhorar o desempenho executando assíncroneamente.

Para executar uma função assíncronea, utilize a `async def` declaração, que executa a função com [assício](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Uma função sem a `async` palavra-chave é executada automaticamente numa piscina de roscas assincio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Use vários processos de trabalho de língua

Por padrão, cada instância de anfitrião de Funções tem um único processo de trabalho de língua. Pode aumentar o número de processos de trabalhador por hospedeiro (até 10) utilizando a definição de aplicação [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores.

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura.

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) argumento na sua assinatura.

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe [**Contexto**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de corda:

`function_directory` O diretório em que a função está a funcionar.

`function_name` Nome da função.

`invocation_id` Identificação da invocação da função atual.

## <a name="global-variables"></a>Variáveis globais

Não é garantido que o estado da sua app será preservado para futuras execuções. No entanto, o tempo de execução das Funções Azure reutiliza frequentemente o mesmo processo para execuções múltiplas da mesma app. Para cache os resultados de um cálculo caro, declare-o como uma variável global.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições de aplicação](functions-app-settings.md), tais como cadeias de ligação de serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições declarando `import os` e, em seguida, utilizando, `setting = os.environ["setting-name"]` .

O exemplo a seguir obtém a [definição de aplicação,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave denominada `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as definições de aplicação são [mantidas no local.settings.jsarquivado](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Versão de Python

A Azure Functions suporta as seguintes versões Python:

| Versão de funções | <sup>*</sup>Versões python |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Distribuição oficial do CPython

Para solicitar uma versão Python específica quando criar a sua aplicação de função em Azure, utilize a `--runtime-version` opção do [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) comando. A versão de tempo de execução de Funções é definida pela `--functions-version` opção. A versão Python é definida quando a aplicação de função é criada e não pode ser alterada.

Ao correr localmente, o tempo de execução utiliza a versão Python disponível.

## <a name="package-management"></a>Gestão de pacotes

Ao desenvolver localmente utilizando as Ferramentas Centrais do Azure Functions ou código de estúdio visual, adicione os nomes e versões das embalagens necessárias ao `requirements.txt` ficheiro e instale-os utilizando- os `pip` .

Por exemplo, o seguinte ficheiro de requisitos e o comando pip podem ser usados para instalar a `requests` embalagem a partir de PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicar no Azure

Quando estiver pronto para publicar, certifique-se de que todas as suas dependências publicamente disponíveis estão listadas no ficheiro requirements.txt, que está localizado na raiz do seu diretório de projeto.

Os ficheiros e pastas do projeto que estão excluídos da publicação, incluindo a pasta de ambiente virtual, estão listados no ficheiro .funcignore.

Existem três ações de construção apoiadas para a publicação do seu projeto Python para a Azure: construção remota, construção local e construções usando dependências personalizadas.

Também pode utilizar pipelines Azure para construir as suas dependências e publicar usando a entrega contínua (CD). Para saber mais, consulte [a entrega contínua utilizando Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Construção remota

Ao utilizar a construção remota, as dependências restauradas no servidor e dependências nativas correspondem ao ambiente de produção. Isto resulta num pacote de implantação menor para carregar. Utilize a construção remota ao desenvolver aplicações Python no Windows. Se o seu projeto tiver dependências personalizadas, pode [utilizar a construção remota com URL de índice extra.](#remote-build-with-extra-index-url)

As dependências são obtidas remotamente com base no conteúdo do ficheiro requirements.txt. [A construção remota](functions-deployment-technologies.md#remote-build) é o método de construção recomendado. Por predefinição, as Ferramentas Principais Azure Functions solicitam uma construção remota quando utiliza o seguinte comando [de publicação func azure functionapp](functions-run-local.md#publish) para publicar o seu projeto Python para a Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome da sua aplicação de função em Azure.

A [extensão de funções Azure para Código do Estúdio Visual](functions-create-first-function-vs-code.md#publish-the-project-to-azure) também solicita uma construção remota por padrão.

### <a name="local-build"></a>Construção local

As dependências são obtidas localmente com base no conteúdo do ficheiro requirements.txt. Pode evitar fazer uma construção remota utilizando o seguinte comando [de publicação func azure functionapp](functions-run-local.md#publish) para publicar com uma construção local.

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome da sua aplicação de função em Azure.

Utilizando a `--build local` opção, as dependências do projeto são lidas a partir do ficheiro requirements.txt e esses pacotes dependentes são descarregados e instalados localmente. Os ficheiros e dependências do projeto são implantados do computador local para o Azure. Isto resulta num pacote de implementação maior a ser enviado para a Azure. Se, por alguma razão, as dependências do seu ficheiro requirements.txt não puderem ser adquiridas pela Core Tools, deve utilizar a opção de dependências personalizadas para publicação.

Não recomendamos a utilização de construções locais quando se desenvolve localmente no Windows.

### <a name="custom-dependencies"></a>Dependências personalizadas

Quando o seu projeto tem dependências não encontradas no [Python Package Index,](https://pypi.org/)existem duas formas de construir o projeto. O método de construção depende de como se constrói o projeto.

#### <a name="remote-build-with-extra-index-url"></a>Construção remota com URL de índice extra

Quando os seus pacotes estiverem disponíveis a partir de um índice de pacote personalizado acessível, utilize uma construção remota. Antes de publicar, certifique-se de [criar uma definição de aplicação](functions-how-to-use-azure-function-app-settings.md#settings) chamada `PIP_EXTRA_INDEX_URL` . O valor desta definição é o URL do seu índice de pacote personalizado. A utilização desta definição diz à construção remota para funcionar `pip install` utilizando a `--extra-index-url` opção. Para saber mais, consulte o [pip Python para instalar documentação.](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)

Também pode utilizar credenciais básicas de autenticação com os seus URLs de índice de pacote extra. Para saber mais, consulte [credenciais básicas de autenticação](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) na documentação Python.

#### <a name="install-local-packages"></a>Instalar pacotes locais

Se o seu projeto utilizar pacotes não disponíveis publicamente para as nossas ferramentas, pode disponibilizá-los para a sua aplicação colocando-os na \_ \_ \_ \_ app/.python_packages diretório. Antes de publicar, executar o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao utilizar dependências personalizadas, deve utilizar `--no-build` a opção de publicação, uma vez que já instalou as dependências na pasta do projeto.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome da sua aplicação de função em Azure.

## <a name="unit-testing"></a>Teste de unidade

As funções escritas em Python podem ser testadas como outros códigos Python utilizando quadros de teste padrão. Para a maioria das encadernações, é possível criar um objeto de entrada simulado criando uma instância de uma classe apropriada a partir do `azure.functions` pacote. Uma vez que a [`azure.functions`](https://pypi.org/project/azure-functions/) embalagem não está imediatamente disponível, certifique-se de instalá-lo através do seu `requirements.txt` ficheiro, conforme descrito na secção de [gestão](#package-management) de pacotes acima.

Por exemplo, segue-se um teste simulado de uma função desencadeada HTTP:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

Aqui está outro exemplo, com uma função despoletamento de fila:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Ficheiros temporários

O `tempfile.gettempdir()` método devolve uma pasta temporária, que no Linux é `/tmp` . A sua aplicação pode utilizar este diretório para armazenar ficheiros temporários gerados e utilizados pelas suas funções durante a execução.

> [!IMPORTANT]
> Os ficheiros escritos para o diretório temporário não são garantidos que persistam em todas as invocações. Durante a escala, os ficheiros temporários não são partilhados entre casos.

O exemplo a seguir cria um ficheiro temporário nomeado no diretório temporário `/tmp` (

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Recomendamos que mantenha os seus testes numa pasta separada da pasta do projeto. Isto impede-o de implementar código de teste com a sua aplicação.

## <a name="preinstalled-libraries"></a>Bibliotecas pré-instaladas

Há algumas bibliotecas com o tempo de funcionamento das Funções Python.

### <a name="python-standard-library"></a>Biblioteca Padrão python

A Biblioteca Padrão python contém uma lista de módulos Python incorporados que são enviados com cada distribuição python. A maioria destas bibliotecas ajuda-o a aceder à funcionalidade do sistema, como o ficheiro I/O. Nos sistemas Windows, estas bibliotecas são instaladas com Python. Nos sistemas baseados no Unix, são fornecidos por coleções de pacotes.

Para ver todos os detalhes da lista destas bibliotecas, visite os links abaixo:

* [Biblioteca Padrão Python 3.6](https://docs.python.org/3.6/library/)
* [Biblioteca Padrão Python 3.7](https://docs.python.org/3.7/library/)
* [Biblioteca Padrão Python 3.8](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Funções Azure Python dependências operárias

O trabalhador de Funções Python requer um conjunto específico de bibliotecas. Você também pode usar estas bibliotecas nas suas funções, mas elas não fazem parte do padrão Python. Se as suas funções dependerem de alguma destas bibliotecas, estas podem não estar disponíveis para o seu código quando estiverem fora das Funções Azure. Pode encontrar uma lista detalhada de dependências na **instalação \_ requer** a secção no [ficheiro setup.py.](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282)

> [!NOTE]
> Se a requirements.txt da sua aplicação de função contiver uma `azure-functions-worker` entrada, remova-a. O trabalhador de funções é gerido automaticamente pela plataforma Azure Functions, e atualizamo-lo regularmente com novas funcionalidades e correções de bugs. Instalar manualmente uma versão antiga do trabalhador em requirements.txt pode causar problemas inesperados.

### <a name="azure-functions-python-library"></a>Biblioteca Azure Functions Python

Cada atualização de trabalhadores python inclui uma nova versão da [biblioteca Azure Functions Python (azure.funções)](https://github.com/Azure/azure-functions-python-library). Esta abordagem facilita a atualização contínua das suas aplicações de função Python, uma vez que cada atualização é compatível com retro-compatível. Uma lista de lançamentos desta biblioteca pode ser encontrada em [PyPi de funções azure.](https://pypi.org/project/azure-functions/#history)

A versão da biblioteca de tempo de execução é fixada pelo Azure, e não pode ser ultrapassada por requirements.txt. A `azure-functions` entrada em requirements.txt é apenas para a linhar e a sensibilização do cliente.

Utilize o seguinte código para rastrear a versão real da biblioteca Python Functions no seu tempo de funcionamento:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Bibliotecas do sistema de tempo de execução

Para obter uma lista de bibliotecas de sistema pré-instaladas em imagens do trabalhador python Docker, siga os links abaixo:

|  Tempo de execução das funções  | Versão debian | Versões python |
|------------|------------|------------|
| Versão 2.x | Esticar  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versão 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Partilha de recursos de várias origens

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

O CORS é totalmente suportado para aplicações de função Python.

## <a name="known-issues-and-faq"></a>Questões conhecidas e FAQ

Segue-se uma lista de guias de resolução de problemas para questões comuns:

* [MóduloNotFoundError e ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Não é possível importar 'cygrpc'](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Todos os problemas e pedidos de funcionalidades conhecidos são rastreados utilizando a lista [de problemas do GitHub.](https://github.com/Azure/azure-functions-python-worker/issues) Se encontrar um problema e não encontrar o problema no GitHub, abra um novo problema e inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Documentação API do pacote de funções AZure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Funções Azure dispara e encaderna](functions-triggers-bindings.md)
* [Encadernações de armazenamento de bolhas](functions-bindings-storage-blob.md)
* [Encadernações HTTP e Webhook](functions-bindings-http-webhook.md)
* [Encadernações de armazenamento de fila](functions-bindings-storage-queue.md)
* [Acionador de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
