---
title: Perfis de memória em aplicativos Python em Funções Azure
description: Aprenda a perfilar o uso da memória de aplicações Python e identifique o estrangulamento da memória.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258814"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Perfil Python aplica o uso da memória em Funções Azure

Durante o desenvolvimento ou depois de implementar o seu projeto de aplicação de função Python local para o Azure, é uma boa prática analisar potenciais estrangulamentos de memória nas suas funções. Tais estrangulamentos podem diminuir o desempenho das suas funções e levar a erros. As seguintes instruções [mostram-lhe](https://pypi.org/project/memory-profiler) como utilizar o pacote Python de perfil de memória, que fornece uma análise de consumo de memória line-by-line das suas funções à medida que executam.

> [!NOTE]
> O perfil de memória destina-se apenas à análise da pegada de memória no ambiente de desenvolvimento. Por favor, não aplique o perfil de memória em aplicações de função de produção.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a desenvolver uma aplicação de função Python, deve cumprir estes requisitos:

* [Python 3.6.x ou superior](https://www.python.org/downloads/release/python-374/). Para verificar a lista completa de versões Python suportadas em Funções Azure, visite o [guia de desenvolvimento python](functions-reference-python.md#python-version).

* A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#v2)

* [Código visual do estúdio](https://code.visualstudio.com/) instalado numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Processo de perfis de memória

1. Na sua requirements.txt, adicione `memory-profiler` para garantir que o pacote será agregado com a sua implantação. Se estiver a desenvolver na sua máquina local, pode querer [ativar um ambiente virtual Python](create-first-function-cli-python.md#create-venv) e fazer uma resolução de pacotes através de `pip install -r requirements.txt` .

2. No seu script de função (geralmente \_ \_ \_ \_ init .py), adicione as seguintes linhas acima da `main()` função. Isto garantirá que o madeireiro-raiz relata os nomes do madeireiro, de modo a que os registos de perfis de memória sejam distinguíveis pelo prefixo `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Exemplo

Aqui está um exemplo de realização de perfis de memória em um assíncronos e um detonador HTTP sincronizado, chamado "HttpTriggerAsync" e "HttpTriggerSync" respectivamente. Vamos construir uma aplicação de função Python que simplesmente envia pedidos GET para a página inicial da Microsoft.

### <a name="create-a-python-function-app"></a>Criar uma aplicação de função Python

Uma aplicação de função Python deve seguir a estrutura de [pastas](functions-reference-python.md#folder-structure)especificadas do Azure Functions . Para andaimes do projeto, recomendamos a utilização das Ferramentas Centrais Azure Functions executando os seguintes comandos:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Atualizar o conteúdo do ficheiro

O requirements.txt define os pacotes que serão utilizados no nosso projeto. Além do Azure Functions SDK e do profiler de memória, introduzimos `aiohttp` para pedidos HTTP assíncronos e `requests` para chamadas HTTP sincronizadas.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Também precisamos de reescrever o gatilho HTTP assíncronos `HttpTriggerAsync/__init__.py` e configurar o perfil de memória, o formato root logger e a ligação de streaming do madeireiro.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Para o gatilho HTTP sincronizado, consulte a `HttpTriggerSync/__init__.py` seguinte secção de código:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>App de função Profile Python em ambiente de desenvolvimento local

Depois de fazer todas as alterações acima, há mais alguns passos para inicializar um envionment virtual Python para o tempo de execução das Funções Azure.

1. Abra uma concha Windows PowerShell ou qualquer outra concha Linux como preferir.
2. Crie um ambiente virtual Python `py -m venv .venv` em Windows ou em `python3 -m venv .venv` Linux.
3. Ative o ambiente virutal Python com `.venv\Scripts\Activate.ps1` no Windows PowerShell ou `source .venv/bin/activate` na casca Linux.
4. Restaurar as dependências python com `pip install requirements.txt`
5. Inicie o tempo de funcionamento das funções Azure localmente com as ferramentas principais das funções Azure `func host start`
6. Envie um pedido GET para `https://localhost:7071/api/HttpTriggerAsync` ou `https://localhost:7071/api/HttpTriggerSync` .
7. Deve apresentar um relatório de perfis de memória semelhante à secção abaixo em Ferramentas Principais de Funções Azure.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento do Azure Functions Python, consulte os seguintes recursos:

* [Guia de desenvolvedores de Azure Functions Python](functions-reference-python.md)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
