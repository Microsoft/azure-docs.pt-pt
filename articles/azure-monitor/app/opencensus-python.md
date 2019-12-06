---
title: Monitorar aplicativos Python com o Azure Monitor (versão prévia) | Microsoft Docs
description: Fornece instruções para conectar o Python OpenCensus com o Azure Monitor
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 2114e60b5ed684063ed100279ea19f561bd335ea
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849790"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurar Azure Monitor para seu aplicativo Python (versão prévia)

O Azure Monitor dá suporte ao rastreamento distribuído, à coleta de métrica e ao registro em log de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io). Este artigo explicará o processo de configuração do OpenCensus para Python e o envio dos dados de monitoramento para Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação do Python. Este artigo usa o [Python 3.7.0](https://www.python.org/downloads/), embora as versões anteriores provavelmente funcionem com pequenas alterações.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Criar um recurso de Application Insights no Azure Monitor

Primeiro, você precisa criar um recurso de Application Insights no Azure Monitor, que irá gerar uma chave de instrumentação (iKey). Em seguida, o iKey é usado para configurar o SDK do OpenCensus para enviar dados de telemetria para Azure Monitor.

1. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   ![Adicionando um recurso de Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

   | Definição        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor global exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para hospedar Application Insights dados |
   | **Localização** | Este dos E.U.A. | Um local perto de você ou próximo de onde seu aplicativo está hospedado |

1. Selecione **Criar**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com SDK OpenCensus Python para Azure Monitor

Instale o OpenCensus Azure Monitor exportadores:

```console
python -m pip install opencensus-ext-azure
```

Para obter uma lista completa de pacotes e integrações, consulte [pacotes OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> O comando `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente `PATH` definida para a instalação do Python. Se você ainda não configurou essa variável, precisará fornecer o caminho completo do diretório onde o executável do Python está localizado. O resultado é um comando como este: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

O SDK usa três Azure Monitor exportadores para enviar tipos diferentes de telemetria para Azure Monitor: rastreamento, métricas e logs. Para obter mais informações sobre esses tipos de telemetria, consulte [visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Use as instruções a seguir para enviar esses tipos de telemetria por meio dos três exportadores.

### <a name="trace"></a>Rastreio

1. Primeiro, vamos gerar alguns dados de rastreamento localmente. Em Python IDLE ou seu editor de preferência, insira o código a seguir.

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

2. A execução do código solicitará repetidamente que você insira um valor. Com cada entrada, o valor será impresso no Shell e o módulo OpenCensus Python gerará uma parte correspondente do `SpanData`. O projeto OpenCensus define um [rastreamento como uma árvore de Spans](https://opencensus.io/core-concepts/tracing/).
    
    ```
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

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, desejamos emitir o `SpanData` para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

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

4. Agora, ao executar o script Python, você ainda deve ser solicitado a inserir valores, mas apenas o valor está sendo impresso no Shell. O `SpanData` criado será enviado para Azure Monitor. Você pode encontrar os dados de span emitidos em `dependencies`.

5. Para obter informações sobre amostragem no OpenCensus, dê uma olhada na [amostragem em OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python).

6. Para obter detalhes sobre a correlação de telemetria em seus dados de rastreamento, dê uma olhada na [correlação de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)do OpenCensus.

### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados de métrica local. Vamos criar uma métrica simples para acompanhar o número de vezes que o usuário pressiona ENTER.

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
2. A execução do código solicitará repetidamente que você pressione Enter. Uma métrica é criada para acompanhar o número de vezes que Enter é pressionado. Com cada entrada, o valor será incrementado e as informações de métrica serão exibidas no console. As informações incluem o valor atual e o carimbo de data/hora atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, desejamos emitir os dados de métrica para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

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

4. O exportador enviará dados de métrica para Azure Monitor em um intervalo fixo. O padrão é a cada 15 segundos. Estamos acompanhando uma única métrica, portanto, esses dados de métrica, com qualquer valor e carimbo de data/hora que ele contém, serão enviados a cada intervalo. Você pode encontrar os dados em `customMetrics`.

### <a name="logs"></a>Registos

1. Primeiro, vamos gerar alguns dados de log locais.

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

2.  O código solicitará continuamente um valor a ser inserido. Uma entrada de log é emitida para cada valor inserido.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, queremos emitir os dados de log para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

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

4. O exportador enviará dados de log para Azure Monitor. Você pode encontrar os dados em `traces`.

5. Para formatar suas mensagens de log, você pode usar `formatters` na API interna de [log](https://docs.python.org/3/library/logging.html#formatter-objects)do Python.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Você também pode adicionar dimensões personalizadas aos seus logs. Eles serão exibidos como pares de chave-valor em `customDimensions` em Azure Monitor.
> [!NOTE]
> Para que esse recurso funcione, você precisa passar um dicionário como um argumento para os logs, qualquer outra estrutura de dados será ignorada. Para manter a formatação da cadeia de caracteres, armazene-as em um dicionário e passe-as como argumentos.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    logger.warning('action', {'key-1': 'value-1', 'key-2': 'value2'})
    ```

7. Para obter detalhes sobre como enriquecer seus logs com dados de contexto de rastreamento, consulte integração do OpenCensus Python [logs](https://docs.microsoft.com/azure/azure-monitor/app/correlation#logs-correlation).

## <a name="view-your-data-with-queries"></a>Exibir seus dados com consultas

Você pode exibir os dados de telemetria que foram enviados do seu aplicativo por meio da guia **logs (análise)** .

![Captura de tela do painel Visão geral com "logs (análise)" selecionado em uma caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **ativo**:

- Para a telemetria enviada com o exportador de rastreamento de Azure Monitor, as solicitações de entrada aparecem em `requests`. As solicitações de saída ou em andamento aparecem em `dependencies`.
- Para a telemetria enviada com a Azure Monitor o exportador de métricas, as métricas enviadas aparecem em `customMetrics`.
- Para telemetria enviada com o exportador de logs de Azure Monitor, os logs aparecem em `traces`. As exceções aparecem em `exceptions`.

Para obter informações mais detalhadas sobre como usar consultas e logs, consulte [logs em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Customization](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integração do Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integração do Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integração do MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passos seguintes

* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas de métrica](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica ultrapassar um limite. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.
