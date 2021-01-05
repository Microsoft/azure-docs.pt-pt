---
title: Melhorar o desempenho das aplicações Python em Funções Azure
description: Aprenda a desenvolver aplicações Azure Functions usando Python que são altamente performantes e escalam bem sob carga.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3383ec322903d15c6090630890d14ce6d90bc96f
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899405"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Melhorar o desempenho das aplicações Python em Funções Azure

Ao desenvolver para funções Azure usando Python, você precisa entender como as suas funções funcionam e como esse desempenho afeta a forma como a sua aplicação de função é dimensionada. A necessidade é mais importante na conceção de aplicações altamente performantes. Os principais fatores a ter em conta ao conceber, escrever e configurar as suas funções são a escala horizontal e as configurações de desempenho de produção.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal
Por predefinição, o Azure Functions monitoriza automaticamente a carga da sua aplicação e cria casos adicionais de anfitrião para Python, conforme necessário. As Funções Azure utilizam limiares incorporados para diferentes tipos de gatilhos para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para o QueueTrigger. Estes limiares não são configuráveis pelo utilizador. Para mais informações, consulte [como funcionam os planos De Consumo e Premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

## <a name="improving-throughput-performance"></a>Melhorar o desempenho da produção

As configurações predefinidas são adequadas para a maioria das aplicações Azure Functions. No entanto, pode melhorar o desempenho da produção das suas aplicações utilizando configurações com base no seu perfil de carga de trabalho. O primeiro passo é compreender o tipo de carga de trabalho que está a correr.

|| Carga de trabalho i/O-bound | Carga de trabalho ligada ao CPU |
|--| -- | -- |
|Características da aplicação de função| <ul><li>A aplicação precisa de lidar com muitas invocações simultâneas.</li> <li> A aplicação processa um grande número de eventos de E/S, tais como chamadas de rede e leitura/escrita de disco.</li> </ul>| <ul><li>A aplicação faz cálculos de longa duração, como o redimensionamento de imagens.</li> <li>A aplicação faz a transformação de dados.</li> </ul> |
|Exemplos| <ul><li>APIs da Web</li><ul> | <ul><li>Processamento de dados</li><li> Inferência de aprendizagem automática</li><ul>|

 
Como as cargas de trabalho da função real são geralmente uma mistura de I/O e CPU ligados, você deve perfilar a app sob cargas de produção realistas.


### <a name="performance-specific-configurations"></a>Configurações específicas do desempenho

Depois de compreender o perfil de carga de trabalho da sua aplicação de função, as seguintes são as configurações que pode utilizar para melhorar o desempenho de produção das suas funções.

* [Async](#async)
* [Trabalhador de linguagem múltipla](#use-multiple-language-worker-processes)
* [Trabalhadores max dentro de um processo de trabalhador de língua](#set-up-max-workers-within-a-language-worker-process)
* [Loop de evento](#managing-event-loop)
* [Escala vertical](#vertical-scaling)



#### <a name="async"></a>Async

Como [Python é um tempo de execução de um único roscado,](https://wiki.python.org/moin/GlobalInterpreterLock)uma instância hospedeira para Python pode processar apenas uma invocação de função de cada vez por defeito. Para aplicações que processam um grande número de eventos de E/S e/ou está ligado a I/O, você pode melhorar o desempenho significativamente executando funções assíncroneamente.

Para executar uma função assíncronea, utilize a `async def` declaração, que executa a função com [assício](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Aqui está um exemplo de uma função com gatilho HTTP que utiliza [cliente aiohttp](https://pypi.org/project/aiohttp/) http:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Uma função sem a `async` palavra-chave é executada automaticamente numa piscina de fio ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Para obter o pleno benefício de executar assíncroneamente, a operação/biblioteca de E/S que é utilizada no seu código também precisa de ter async implementado. A utilização de operações de E/S sincronizadas em funções definidas como assíncronos **pode prejudicar** o desempenho geral. Se as bibliotecas que está a utilizar não tiverem a versão async implementada, poderá ainda beneficiar de executar o seu código assíncronosamente, [gerindo o loop](#managing-event-loop) de eventos na sua aplicação. 

Aqui estão alguns exemplos de bibliotecas de clientes que implementaram o padrão async:
- [aiohttp](https://pypi.org/project/aiohttp/) - Http cliente/servidor para assíncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) - Primitivos de alto nível assínc/aguardados prontos para trabalhar com ligação à rede
- [Janus Queue](https://pypi.org/project/janus/) - Fila segura em linha para Python
- [pyzmq](https://pypi.org/project/pyzmq/) - Ligações python para ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Compreensão de async no trabalhador python

Quando se define `async` na frente de uma assinatura de função, python marcará a função como uma coroutina. Ao chamar a coroutina, pode ser agendada como uma tarefa para um ciclo de eventos. Quando convoca `await` uma função assínca, regista uma continuação no ciclo de eventos e permite que o ciclo de eventos processe a próxima tarefa durante o tempo de espera.

No nosso Python Worker, o trabalhador partilha o ciclo do evento com a função do cliente `async` e é capaz de lidar com vários pedidos simultaneamente. Encorajamos fortemente os nossos clientes a utilizarem bibliotecas compatíveis com assício (por exemplo, [aiohttp,](https://pypi.org/project/aiohttp/) [pyzmq).](https://pypi.org/project/pyzmq/) A utilização destas recomendações aumentará consideravelmente a produção da sua função em comparação com as bibliotecas implementadas de forma sincronizada.

> [!NOTE]
>  Se a sua função for declarada como `async` sem qualquer dentro da sua `await` implementação, o desempenho da sua função será severamente impactado, uma vez que o loop do evento será bloqueado que proíbe o trabalhador pitão de lidar com pedidos simultâneos.

#### <a name="use-multiple-language-worker-processes"></a>Use vários processos de trabalho de língua

Por padrão, cada instância de anfitrião de Funções tem um único processo de trabalho de língua. Pode aumentar o número de processos de trabalhador por hospedeiro (até 10) utilizando a definição de aplicação [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores.

Para as aplicações ligadas ao CPU, deve definir o número de trabalhadores linguísticos como ou superiores ao número de núcleos disponíveis por aplicação de função. Para saber mais, consulte [a instância disponível SKUs](functions-premium-plan.md#available-instance-skus). 

As aplicações ligadas à I/O também podem beneficiar do aumento do número de processos de trabalhadores para além do número de núcleos disponíveis. Tenha em mente que definir o número de trabalhadores demasiado elevado pode ter impacto no desempenho global devido ao aumento do número de interruptores de contexto necessários. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Criar trabalhadores max dentro de um processo de trabalhador de língua

Como mencionado na [secção](#understanding-async-in-python-worker)async, o trabalhador da língua python trata as funções e [coroutinas de](https://docs.python.org/3/library/asyncio-task.html#coroutines) forma diferente. Uma coroutina é executada dentro do mesmo ciclo de eventos em que o trabalhador da língua funciona. Por outro lado, uma invocação de função é executada dentro de um [ThreadPoolExecutor,](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)que é mantido pelo trabalhador da língua, como um fio.

Pode definir o valor dos trabalhadores máximos permitidos para executar funções de sincronização utilizando a definição de aplicação [PYTHON_THREADPOOL_THREAD_COUNT.](functions-app-settings.md#python_threadpool_thread_count) Este valor define o `max_worker` argumento do objeto ThreadPoolExecutor, que permite que python use uma piscina de na maioria dos `max_worker` fios para executar chamadas assíncroneas. O `PYTHON_THREADPOOL_THREAD_COUNT` se aplica a cada trabalhador que o hospedeiro functions cria, e python decide quando criar um novo fio ou reutilizar o fio ocioso existente. Para as versões Python mais antigas (isto é, `3.8` , e - o valor está definido para `3.7` `3.6` `max_worker` 1. Para a versão Python `3.9` , está definido para `max_worker` `None` .

Para aplicações com ligação ao CPU, deve manter a definição para um número baixo, a partir de 1 e aumentando à medida que experimenta com a sua carga de trabalho. Esta sugestão é reduzir o tempo gasto em interruptores de contexto e permitir que tarefas ligadas à CPU terminem.

Para aplicações ligadas a I/O, você deve ver ganhos substanciais aumentando o número de fios trabalhando em cada invocação. a recomendação é começar com o padrão Python - o número de núcleos + 4 e, em seguida, ajustar com base nos valores de produção que você está vendo.

Para misturar aplicações de cargas de trabalho, deve equilibrar tanto `FUNCTIONS_WORKER_PROCESS_COUNT` e `PYTHON_THREADPOOL_THREAD_COUNT` configurações para maximizar a produção. Para entender em que as suas aplicações de função passam mais tempo, recomendamos que as perfilem e desfaçam os valores de acordo com o comportamento que apresentam. Consulte também esta [secção](#use-multiple-language-worker-processes) para saber mais sobre FUNCTIONS_WORKER_PROCESS_COUNT configurações de aplicações.

> [!NOTE]
>  Embora estas recomendações se apliquem tanto a funções de http como não-HTTP, poderá ser necessário ajustar outras configurações específicas do gatilho para funções não-HTTP ativadas para obter o desempenho esperado das suas aplicações de função. Para mais informações sobre isto, consulte este [artigo.](functions-best-practices.md)


#### <a name="managing-event-loop"></a>Loop de eventos de gestão

Deve utilizar bibliotecas de terceiros compatíveis com assícios. Se nenhuma das bibliotecas de terceiros satisfaça as suas necessidades, também pode gerir os ciclos de eventos em Azure Functions. Gerir os ciclos de eventos dá-lhe mais flexibilidade na gestão de recursos computativos, e também permite envolver bibliotecas de I/O sincronizadas em coroutinas.

Existem muitos documentos oficiais úteis python discutindo os [Coroutines e Tasks](https://docs.python.org/3/library/asyncio-task.html) e [Event Loop](https://docs.python.org/3.8/library/asyncio-eventloop.html) usando a biblioteca **de assício** incorporada.

Tome como exemplo a biblioteca de [pedidos](https://github.com/psf/requests) que se segue, este código corta-códigos utiliza a biblioteca **assício** para envolver o `requests.get()` método numa coroutina, executando vários pedidos web para SAMPLE_URL simultaneamente.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Dimensionamento vertical
Para mais unidades de processamento especialmente em operação ligada à CPU, você pode ser capaz de obter isso através da atualização para plano premium com especificações mais elevadas. Com unidades de processamento mais elevadas, pode ajustar o número de trabalhos de contagem de trabalho de acordo com o número de núcleos disponíveis e obter um maior grau de paralelismo. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento do Azure Functions Python, consulte os seguintes recursos:

* [Guia de desenvolvedores de Azure Functions Python](functions-reference-python.md)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)

