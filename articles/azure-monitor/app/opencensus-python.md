---
title: Monitor Python aplicações com Azure Monitor / Microsoft Docs
description: Fornece instruções para ligar o OpenCensus Python com o Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 1e6376cd8389a4f1f0defebce0a2c7b6d0f9deed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323270"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurar o Azure Monitor para a sua aplicação Python

O Azure Monitor suporta o rastreio distribuído, a recolha métrica e o registo de aplicações Python através da integração com [o OpenCensus](https://opencensus.io). Este artigo acompanha-o através do processo de criação do OpenCensus para Python e do envio dos seus dados de monitorização para o Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação Python. Este artigo utiliza [Python 3.7.0, embora](https://www.python.org/downloads/release/python-370/)outras versões provavelmente funcionem com pequenas alterações. O SDK só suporta Python v2.7 e v3.4-v3.7.
- Criar um [recurso](./create-new-resource.md)Application Insights . Será-lhe atribuída a sua própria chave de instrumentação (ikey) para o seu recurso.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com OpenCensus Python SDK para monitor Azure

Instale os exportadores do Monitor OpenCensus Azure:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> O `python -m pip install opencensus-ext-azure` comando pressupõe que tem uma `PATH` variável ambiental definida para a sua instalação Python. Se ainda não configuraste esta variável, tens de dar o caminho completo do diretório para onde está o teu Python. O resultado é um comando como este: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

O SDK utiliza três exportadores do Azure Monitor para enviar diferentes tipos de telemetria ao Azure Monitor. São vestígios, métricas e troncos. Para obter mais informações sobre estes tipos de telemetria, consulte [a visão geral da plataforma de dados](../platform/data-platform.md). Utilize as seguintes instruções para enviar estes tipos de telemetria através dos três exportadores.

## <a name="telemetry-type-mappings"></a>Mapeamentos do tipo de telemetria

Aqui estão os exportadores que o OpenCensus fornece mapeados aos tipos de telemetria que se vê no Azure Monitor.

| Pilar da observabilidade | Tipo de telemetria no Monitor Azure    | Explicação                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Registos                    | Vestígios, exceções, costumesEvents   | Telemetria de log, telemetria de exceção, telemetria de evento |
| Métricas                 | customMetrics, performanceCounters | Contadores de desempenho de métricas personalizadas                |
| Rastreio                 | Pedidos de dependências             | Pedidos de entrada, pedidos de saída                |

### <a name="logs"></a>Registos

1. Primeiro, vamos gerar alguns dados de registo local.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. O código solicita continuamente a entrada de um valor. É emitida uma entrada de registo para cada valor introduzido.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Embora a introdução de valores seja útil para fins de demonstração, em última análise queremos emitir os dados de registo para o Azure Monitor. Passe a sua ligação diretamente para o exportador. Ou, pode especificar numa variável ambiental, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. O exportador envia dados de registo para o Azure Monitor. Pode encontrar os dados em `traces` . 

    > [!NOTE]
    > Neste contexto, `traces` não é o mesmo `tracing` que. Aqui, `traces` refere-se ao tipo de telemetria que verá no Azure Monitor quando utilizar `AzureLogHandler` . Mas `tracing` refere-se a um conceito no OpenCensus e diz respeito ao [rastreio distribuído.](./distributed-tracing.md)

    > [!NOTE]
    > O madeireiro de raiz está configurado com o nível de ADVERTÊNCIA. Isso significa que quaisquer registos que envies que tenham menos gravidade são ignorados, e por sua vez, não serão enviados para o Monitor Azure. Para mais informações, consulte [a documentação.](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)

1. Também pode adicionar propriedades personalizadas às suas mensagens de registo no argumento *extra* de palavras-chave, utilizando o campo custom_dimensions. Estas propriedades aparecem como pares de valor-chave `customDimensions` no Azure Monitor.
    > [!NOTE]
    > Para que esta funcionalidade funcione, tem de passar um dicionário para o campo custom_dimensions. Se passar argumentos de qualquer outro tipo, o madeireiro ignora-os.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Configurar a exploração madeireira para aplicações de Django

Pode configurar o registo explicitamente no seu código de aplicação como acima para as suas aplicações Django, ou pode especificá-lo na configuração de registo registado do Django. Este código pode entrar em qualquer ficheiro que utilize para configuração de configurações de Configurações de Django. Para configurar as definições de Django, consulte as [definições de Django](https://docs.djangoproject.com/en/3.0/topics/settings/). Para obter mais informações sobre a configuração da exploração madeireira, consulte [a exploração madeireira de Django.](https://docs.djangoproject.com/en/3.0/topics/logging/)

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Certifique-se de que utiliza o madeirão com o mesmo nome especificado na sua configuração.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Enviar exceções

O OpenCensus Python não rastreia e envia `exception` telemetria automaticamente. São enviados `AzureLogHandler` usando exceções através da biblioteca de registo python. Pode adicionar propriedades personalizadas como com a sessão normal.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Como deve registar as exceções explicitamente, cabe ao utilizador saber como pretende registar exceções não manipuladas. O OpenCensus não coloca restrições na forma como um utilizador quer fazer isto, desde que faça um registo explícito de uma telemetria de exceção.

#### <a name="send-events"></a>Enviar eventos

Pode enviar `customEvent` telemetria exatamente da mesma forma que envia `trace` telemetria, exceto `AzureEventHandler` utilizando.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Amostragem

Para obter informações sobre a amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlação de registos

Para obter detalhes sobre como enriquecer os seus registos com dados de contexto de traços, consulte a [integração de registos OpenCensus](./correlation.md#log-correlation)Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter mais informações sobre como modificar a telemetria rastreada antes de ser enviada para o Azure Monitor, consulte os [processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)OpenCensus Python .


### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados métricos locais. Criaremos uma métrica simples para rastrear o número de vezes que o utilizador seleciona a tecla **'Entrar'.**

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Executar o código solicita-lhe repetidamente que **selecione Enter**. Uma métrica é criada para rastrear o número de vezes **que Enter** é selecionado. A cada entrada, o valor é incrementado e a informação métrica aparece na consola. A informação inclui o valor atual e o carimbo de hora atual quando a métrica foi atualizada.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Embora a introdução de valores seja útil para fins de demonstração, em última análise queremos emitir os dados métricos para o Azure Monitor. Passe a sua ligação diretamente para o exportador. Ou, pode especificar numa variável ambiental, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. O exportador envia dados métricos ao Azure Monitor num intervalo fixo. O padrão é a cada 15 segundos. Estamos a seguir uma única métrica, por isso estes dados métricos, com o valor e o carimbo de tempo que contém, são enviados a cada intervalo. Pode encontrar os dados em `customMetrics` .

#### <a name="performance-counters"></a>Contadores de desempenho

Por predefinição, o exportador de métricas envia um conjunto de contadores de desempenho para o Azure Monitor. Pode desativá-lo colocando `enable_standard_metrics` a bandeira no construtor do exportador de `False` métricas.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Estes contadores de desempenho são atualmente enviados:

- Memória Disponível (bytes)
- Tempo do processador CPU (percentagem)
- Taxa de Pedido de Entrada (por segundo)
- Pedido de entrada Tempo médio de execução (milissegundos)
- Processo CPU Utilização (percentagem)
- Processe Bytes Privados (bytes)

Você deve ser capaz de ver estas métricas em `performanceCounters` . Para mais informações, consulte [os contadores de desempenho.](./performance-counters.md)

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter informações sobre como modificar a telemetria rastreada antes de ser enviada para o Azure Monitor, consulte os [processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)OpenCensus Python .

### <a name="tracing"></a>Rastreio

> [!NOTE]
> No OpenCensus, `tracing` refere-se ao [rastreio distribuído.](./distributed-tracing.md) Os `AzureExporter` envios `requests` e `dependency` telemetria para o Monitor Azure.

1. Primeiro, vamos gerar alguns dados de vestígios localmente. Em Python IDLE, ou o seu editor de eleição, insira o seguinte código:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Executar o código solicita-lhe repetidamente que introduza um valor. A cada entrada, o valor é impresso na concha. O Módulo Python OpenCensus gera uma peça correspondente de `SpanData` . O projeto OpenCensus define um [traço como uma árvore de vãos.](https://opencensus.io/core-concepts/tracing/)
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Embora a entrada de valores seja útil para fins de demonstração, em última análise queremos emitir `SpanData` para o Azure Monitor. Passe a sua ligação diretamente para o exportador. Ou, pode especificar numa variável ambiental, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Agora, quando executar o script Python, você ainda deve ser solicitado a introduzir valores, mas apenas o valor está sendo impresso na concha. O criado `SpanData` é enviado para o Azure Monitor. Pode encontrar os dados de envergadura emitidos em `dependencies` . Para obter mais informações sobre pedidos de saída, consulte as [dependências](./opencensus-python-dependency.md)do OpenCensus Python.
Para obter mais informações sobre os pedidos de entrada, consulte os pedidos do [OpenCensus](./opencensus-python-request.md)Python .

#### <a name="sampling"></a>Amostragem

Para obter informações sobre a amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlação de vestígios

Para obter mais informações sobre a correlação de telemetria nos seus dados de rastreio, dê uma olhada na [correlação de telemetria](./correlation.md#telemetry-correlation-in-opencensus-python)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter mais informações sobre como modificar a telemetria rastreada antes de ser enviada para o Azure Monitor, consulte [os processadores de telemetria](./api-filtering-sampling.md#opencensus-python-telemetry-processors)OpenCensus Python .

## <a name="configure-azure-monitor-exporters"></a>Configuure Azure Monitor exportadores

Como mostrado, existem três diferentes exportadores do Azure Monitor que apoiam o OpenCensus. Cada um envia diferentes tipos de telemetria para o Azure Monitor. Para ver que tipo de telemetria cada exportador envia, consulte a seguinte lista.

Cada exportador aceita os mesmos argumentos para a configuração, passados pelos construtores. Pode ver detalhes sobre cada um aqui:

- `connection_string`: O fio de ligação utilizado para ligar ao seu recurso Azure Monitor. Tem prioridade sobre `instrumentation_key` .
- `enable_standard_metrics`: Usado para `AzureMetricsExporter` . Sinaliza o exportador para enviar automaticamente métricas [de contador de desempenho](../platform/app-insights-metrics.md#performance-counters) para o Azure Monitor. Incumprimentos a `True` .
- `export_interval`: Utilizado para especificar a frequência em segundos de exportação.
- `instrumentation_key`: A tecla de instrumentação utilizada para ligar ao seu recurso Azure Monitor.
- `logging_sampling_rate`: Usado para `AzureLogHandler` . Fornece uma taxa de amostragem [0,1.0] para os registos de exportação. Predefinições para 1.0.
- `max_batch_size`: Especifica o tamanho máximo de telemetria que é exportado de uma só vez.
- `proxies`: Especifica uma sequência de proxies a utilizar para o envio de dados para o Azure Monitor. Para mais informações, consulte [os proxies.](https://requests.readthedocs.io/en/master/user/advanced/#proxies)
- `storage_path`: Um caminho para onde existe a pasta de armazenamento local (telemetria não-ent). A partir de `opencensus-ext-azure` v1.0.3, o caminho padrão é o diretório temporário OS + `opencensus-python`  +  `your-ikey` . Antes de v1.0.3, o caminho padrão é $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Ver os seus dados com consultas

Pode ver os dados de telemetria enviados a partir da sua aplicação através do **separador Logs (Analytics).**

![Screenshot do painel de visão geral com "Logs (Analytics)" selecionado numa caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **Ativo**:

- No que diz a telemetria enviada com o exportador de vestígios do Azure Monitor, os pedidos de entrada aparecem em `requests` . Os pedidos de saída ou em processo aparecem em `dependencies` .
- Para a telemetria enviada com o exportador de métricas Azure Monitor, as métricas enviadas aparecem em `customMetrics` .
- Para a telemetria enviada com o exportador de registos Azure Monitor, os registos aparecem em `traces` . Exceções aparecem em `exceptions` .

Para obter informações mais detalhadas sobre como utilizar consultas e registos, consulte [Logs in Azure Monitor](../platform/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus for Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor Exportadores em GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrações OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Aplicações de amostra do monitor Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passos seguintes

* [Rastreio de pedidos de entrada](./opencensus-python-dependency.md)
* [Rastreio de pedidos de saída](./opencensus-python-request.md)
* [Mapa de aplicação](./app-map.md)
* [Monitorização de desempenho de ponta a ponta](../learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](./monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](./proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas métricos](../platform/alerts-log.md): Desave os alertas para o avisar se uma métrica atravessar um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

