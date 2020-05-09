---
title: Referência de desenvolvedor python para funções Azure
description: Entenda como desenvolver funções com Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: ea128fc7c68b49fc14d796e9a3b91a9dbddd9b26
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780050"
---
# <a name="azure-functions-python-developer-guide"></a>Guia de desenvolvimento de funções azure Python

Este artigo é uma introdução ao desenvolvimento de Funções Azure usando Python. O conteúdo abaixo assume que já leu o guia de desenvolvedores de [Funções Azure](functions-reference.md). 

Para projetos de amostra de função autónoma em Python, consulte as amostras de [Funções Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modelo de programação

A Azure Functions espera que uma função seja um método apátrida no seu script Python que processa a entrada e produz a saída. Por padrão, o prazo de execução espera que `main()` o `__init__.py` método seja implementado como um método global chamado no ficheiro. Também pode [especificar um ponto](#alternate-entry-point)de entrada alternativo .

Os dados dos gatilhos e encadernações `name` estão ligados à função através de atributos de método utilizando a propriedade definida no ficheiro *função.json.* Por exemplo, a _função.json_ abaixo descreve uma função simples desencadeada por um pedido HTTP chamado: `req`

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nesta `__init__.py` definição, o ficheiro que contém o código de função pode parecer o seguinte exemplo:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Também pode declarar explicitamente os tipos de atributos e o tipo de devolução na função utilizando anotações do tipo Python. Isto ajuda-o a usar as funcionalidades intellisense e autocomplete fornecidas por muitos editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilize as anotações Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para ligar a entrada e as saídas aos seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Pode alterar o comportamento padrão de uma função especificando opcionalmente as `scriptFile` propriedades `entryPoint` e propriedades no ficheiro *função.json.* Por exemplo, a _função.json_ abaixo indica `customentry()` o tempo de funcionamento para utilizar o método no ficheiro _main.py,_ como ponto de entrada para a sua Função Azure.

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

A estrutura de pasta recomendada para um projeto funções Python parece ser o seguinte exemplo:

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
A pasta principal\_\_\_\_do projeto (app) pode conter os seguintes ficheiros:

* *local.settings.json*: Usado para armazenar definições de aplicativos e cordas de ligação quando funciona localmente. Este ficheiro não é publicado para o Azure. Para saber mais, consulte [local.settings.file](functions-run-local.md#local-settings-file).
* *requisitos.txt*: Contém a lista de pacotes que o sistema instala na publicação para o Azure.
* *host.json*: Contém opções de configuração global que afetam todas as funções numa aplicação de função. Este ficheiro é publicado no Azure. Nem todas as opções são suportadas quando se corre localmente. Para saber mais, consulte [host.json](functions-host-json.md).
* *.funcignore*: (Opcional) declara ficheiros que não devem ser publicados no Azure.
* *.gitignore*: (Opcional) declara ficheiros excluídos de um repo git, tais como local.settings.json.
* *Dockerfile*: (Opcional) utilizado ao publicar o seu projeto num [recipiente personalizado](functions-create-function-linux-custom-image.md).

Cada função tem o seu próprio ficheiro de código e ficheiro de configuração de ligação (função.json). 

Ao implementar o seu projeto numa aplicação de função no Azure, todo o conteúdo da pasta principal*\_\_(app)\_* deve ser incluído no pacote, mas não na própria pasta. Recomendamos que mantenha os seus testes numa pasta separada `tests`da pasta do projeto, neste exemplo . Isto impede-o de implementar código de teste com a sua aplicação. Para mais informações, consulte [O Teste da Unidade](#unit-testing).

## <a name="import-behavior"></a>Comportamento de importação

Pode importar módulos no seu código de função utilizando referências relativas e absolutas explícitas. Com base na estrutura da pasta acima apresentada, as seguintes importações funcionam a partir da * \_ \_aplicação\_\_\_\_\\\_\_\_* de ficheiros de funções \my first function _ init .py :

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

O código partilhado deve ser mantido numa pasta separada na * \_ \_aplicação\_*. Para os módulos de referência na pasta de *código partilhado,\_* pode utilizar a seguinte sintaxe:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Gatilhos e Inputs

As inputs dividem-se em duas categorias em Funções Azure: entrada do gatilho e entrada adicional. Embora sejam diferentes `function.json` no ficheiro, o uso é idêntico no código Python.  As cordas de ligação ou segredos para `local.settings.json` o gatilho e as fontes de entrada mapeiam os valores do ficheiro quando estão a ser geridos localmente e as definições de aplicação quando estão a ser recorridas em Azure. 

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

Quando a função é invocada, o pedido `req`HTTP é passado para a função como . Uma entrada será recuperada do Armazenamento de Blob Azure com base `obj` no _ID_ no URL da rota e disponibilizada como no corpo de função.  Aqui, a conta de armazenamento especificada é a cadeia de ligação encontrada na definição de aplicações AzureWebJobsStorage, que é a mesma conta de armazenamento usada pela aplicação de função.


## <a name="outputs"></a>Saídas

A saída pode ser expressa tanto no valor de retorno como nos parâmetros de saída. Se houver apenas uma saída, recomendamos a utilização do valor de devolução. Para várias saídas, terá de utilizar parâmetros de saída.

Para utilizar o valor de devolução de uma `name` função como valor de `$return` `function.json`uma ligação de saída, a propriedade da encadernação deve ser fixada em .

Para produzir várias saídas, utilize o `set()` método fornecido pela [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interface para atribuir um valor à ligação. Por exemplo, a função seguinte pode empurrar uma mensagem para uma fila e também devolver uma resposta HTTP.

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

O acesso ao logger de tempo de funcionamento das Funções Azure está disponível através de um manipulador de raiz [`logging`](https://docs.python.org/3/library/logging.html#module-logging) na sua aplicação de função. Este madeireiro está ligado a Insights de Aplicação e permite-lhe sinalizar avisos e erros encontrados durante a execução da função.

O exemplo seguinte regista uma mensagem de informação quando a função é invocada através de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Existem métodos adicionais de registo que permitem escrever para a consola em diferentes níveis de vestígios:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Escreve uma mensagem com nível CRITICAL no tronco de raiz.  |
| **`error(_message_)`**   | Escreve uma mensagem com erro de nível no tronco de raiz.    |
| **`warning(_message_)`**    | Escreve uma mensagem com nível AVISO no tronco de raiz.  |
| **`info(_message_)`**    | Escreve uma mensagem com informação de nível no tronco de raiz.  |
| **`debug(_message_)`** | Escreve uma mensagem com DEBUG nivelado no tronco de raiz.  |

Para saber mais sobre a exploração madeireira, consulte as [Funções Monitor Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP Gatilho e encadernações

O gatilho HTTP é definido no ficheiro função.jon. A `name` ligação deve coincidir com o parâmetro nomeado na função. Nos exemplos anteriores, `req` é utilizado um nome vinculativo. Este parâmetro é um objeto [HttpRequest,] e um objeto [HttpResponse] é devolvido.

A partir do objeto [HttpRequest,] você pode obter cabeçalhos de pedido, parâmetros de consulta, parâmetros de rota e o corpo de mensagem. 

O exemplo que se segue é do modelo de [gatilho HTTP para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

Nesta função, o `name` valor do parâmetro de consulta `params` é obtido a partir do parâmetro do objeto [HttpRequest.] O corpo de mensagem codificado pela JSON é lido utilizando o `get_json` método. 

Da mesma forma, `status_code` pode `headers` definir a mensagem de resposta e para a mensagem de resposta no objeto [httpResponse] devolvido.

## <a name="scaling-and-concurrency"></a>Escala e conmoeda

Por predefinição, as Funções Azure monitorizam automaticamente a carga na sua aplicação e criam instâncias adicionais para python, se necessário. Funções utilizam limiares incorporados (não configuráveis pelo utilizador) para diferentes tipos de gatilhos para decidir quando adicionar instâncias, tais como a idade das mensagens e o tamanho da fila para o QueueTrigger. Para mais informações, consulte [como funcionam os planos de Consumo e Premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Este comportamento de escala é suficiente para muitas aplicações. No entanto, as aplicações com qualquer uma das seguintes características não podem ser dimensionadas de forma tão eficaz:

- A aplicação precisa de lidar com muitas invocações simultâneas.
- A aplicação processa um grande número de eventos de E/O.
- A aplicação está ligada a I/O.

Nesses casos, pode melhorar ainda mais o desempenho utilizando padrões de asincronização e utilizando múltiplos processos de trabalhadores linguísticos.

### <a name="async"></a>Async

Como python é um tempo de execução de um fio único, um exemplo de anfitrião para Python pode processar apenas uma invocação de função de cada vez. Para aplicações que processam um grande número de eventos de I/O e/ou está ligado a I/O, você pode melhorar o desempenho executando as funções assincronicamente.

Para executar uma função assíncrona, utilize a `async def` declaração, que executa a função com [asincronio](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Uma função `async` sem a palavra-chave é executada automaticamente numa piscina de fios asíncrona:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Utilize vários processos de trabalhador estoireiro linguístico

Por padrão, cada instância de anfitrião de Funções tem um único processo de trabalhador linguístico. Pode aumentar o número de processos de trabalhador por hospedeiro (até 10) utilizando a definição de aplicação [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura. 

## <a name="context"></a>Contexto

Para obter o contexto de invocação de [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) uma função durante a execução, inclua o argumento na sua assinatura. 

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de cadeia:

`function_directory`  
O diretório em que a função está a decorrer.

`function_name`  
Nome da função.

`invocation_id`  
Identificação da invocação da função atual.

## <a name="global-variables"></a>Variáveis globais

Não é garantido que o estado da sua aplicação seja preservado para futuras execuções. No entanto, o tempo de funcionamento das Funções Azure reutiliza frequentemente o mesmo processo para múltiplas execuções da mesma app. Para cache os resultados de um cálculo caro, declará-lo como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicação , tais como cordas de ligação de serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas `import os` definições declarando e, em seguida, usando. `setting = os.environ["setting-name"]`

O exemplo seguinte obtém a definição de [aplicação,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave denominada: `myAppSetting`

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as definições de aplicação são [mantidas no ficheiro local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versão de Python 

A Azure Functions suporta as seguintes versões Python:

| Versão funções | Versões Python<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Distribuição oficial do CPython

Para solicitar uma versão Python específica quando criar a `--runtime-version` sua aplicação de funções no Azure, utilize a opção do [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) comando. A versão functions de tempo `--functions-version` de execução é definida pela opção. A versão Python é definida quando a aplicação de funções é criada e não pode ser alterada.  

Ao correr localmente, o tempo de execução utiliza a versão Python disponível. 

## <a name="package-management"></a>Gestão de pacotes

Ao desenvolver localmente as Funções Azure Core Tools ou Visual Studio Code, adicione os `requirements.txt` nomes e `pip`versões dos pacotes necessários ao ficheiro e instale-os utilizando . 

Por exemplo, os seguintes requisitos de ficheiro `requests` e comando pip podem ser utilizados para instalar a embalagem a partir do PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicar no Azure

Quando estiver pronto para publicar, certifique-se de que todas as suas dependências publicamente disponíveis estão listadas no ficheiro requirements.txt, que está localizado na raiz do seu diretório de projeto. 

Os ficheiros e pastas do projeto que estão excluídos da publicação, incluindo a pasta do ambiente virtual, estão listados no ficheiro .funcignore.

Existem três ações de construção apoiadas para a publicação do seu projeto Python para o Azure:

+ Construção remota: As dependências são obtidas remotamente com base no conteúdo do ficheiro requirements.txt. [A construção remota](functions-deployment-technologies.md#remote-build) é o método de construção recomendado. O controlo remoto é também a opção de construção padrão da ferramenta Azure. 
+ Construção local: As dependências são obtidas localmente com base no conteúdo do ficheiro requirements.txt. 
+ Dependências personalizadas: O seu projeto utiliza pacotes não disponíveis publicamente para as nossas ferramentas. (Requer Docker.)

Para construir as suas dependências e publicar utilizando um sistema de entrega contínua (CD), [utilize pipelines Azure](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Construção remota

Por padrão, o Azure Functions Core Tools solicita uma construção remota quando utilizar o seguinte [func azure functionapp publicar](functions-run-local.md#publish) comando para publicar o seu projeto Python para o Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se `<APP_NAME>` de substituir pelo nome da sua aplicação de funções em Azure.

A extensão de [funções Azure para código](functions-create-first-function-vs-code.md#publish-the-project-to-azure) de estúdio visual também solicita uma construção remota por padrão. 

### <a name="local-build"></a>Construção local

Pode evitar fazer uma construção remota utilizando o seguinte [func azure functionapp publicar](functions-run-local.md#publish) comando para publicar com uma construção local. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se `<APP_NAME>` de substituir pelo nome da sua aplicação de funções em Azure. 

Utilizando `--build local` a opção, as dependências do projeto são lidas a partir dos requisitos.txt ficheiro e esses pacotes dependentes são descarregados e instalados localmente. Os ficheiros e dependências do projeto são implantados do seu computador local para o Azure. Isto resulta no envio de um pacote de implantação maior para o Azure. Se, por alguma razão, as dependências dos seus requisitos.ficheiro.txt não puderem ser adquiridos pela Core Tools, deve utilizar a opção de dependências personalizadas para publicação. 

### <a name="custom-dependencies"></a>Dependências personalizadas

Se o seu projeto utilizar pacotes não disponíveis publicamente para as nossas ferramentas, pode disponibilizá-los à sua app colocando-os \_ \_na app\_\_/.python_packages diretório. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao utilizar dependências personalizadas, `--no-build` deve utilizar a opção de publicação, uma vez que já instalou as dependências.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se `<APP_NAME>` de substituir pelo nome da sua aplicação de funções em Azure.

## <a name="unit-testing"></a>Teste de Unidade

As funções escritas em Python podem ser testadas como outro código Python usando quadros de teste padrão. Para a maioria das encadernações, é possível criar um objeto `azure.functions` de entrada simulado criando uma instância de uma classe apropriada a partir do pacote. Uma [`azure.functions`](https://pypi.org/project/azure-functions/) vez que o pacote não está imediatamente `requirements.txt` disponível, certifique-se de que o instala através do seu ficheiro, conforme descrito na secção de gestão de [pacotes](#package-management) acima. 

Por exemplo, segue-se um teste simulado de uma função ativada em HTTP:

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

Aqui está outro exemplo, com uma função desencadeada pela fila:

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

O `tempfile.gettempdir()` método devolve uma pasta temporária, `/tmp`que no Linux é . A sua aplicação pode utilizar este diretório para armazenar ficheiros temporários gerados e utilizados pelas suas funções durante a execução. 

> [!IMPORTANT]
> Os ficheiros escritos ao diretório temporário não são garantidos para persistir em invocações. Durante a escala, os ficheiros temporários não são partilhados entre casos. 

O exemplo seguinte cria um ficheiro temporário`/tmp`nomeado no diretório temporário ( ):

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

## <a name="cross-origin-resource-sharing"></a>Partilha de recursos de várias origens

A Azure Functions suporta a partilha de recursos de origem cruzada (CORS). O CORS está configurado [no portal](functions-how-to-use-azure-function-app-settings.md#cors) e através do [Azure CLI](/cli/azure/functionapp/cors). A lista de origens permitida seleções do CORS aplica-se ao nível da aplicação de funções. Com cors ativado, as `Access-Control-Allow-Origin` respostas incluem o cabeçalho. Para obter mais informações, consulte [Partilha de recursos de várias origens](functions-how-to-use-azure-function-app-settings.md#cors). 

O CORS é totalmente suportado para aplicações de função Python.

## <a name="known-issues-and-faq"></a>Questões conhecidas e FAQ

Todos os problemas conhecidos e pedidos de funcionalidades são rastreados usando a lista de [problemas gitHub.](https://github.com/Azure/azure-functions-python-worker/issues) Se tiver um problema e não encontrar o problema no GitHub, abra um novo problema e inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Documentação api do pacote funções Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* [Encadernações de armazenamento blob](functions-bindings-storage-blob.md)
* [Ligações HTTP e Webhook](functions-bindings-http-webhook.md)
* [Encadernações de armazenamento de fila](functions-bindings-storage-queue.md)
* [Acionador de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
