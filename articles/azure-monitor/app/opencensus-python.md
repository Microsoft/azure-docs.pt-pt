---
title: Monitor Python aplicações com Monitor Azure (pré-visualização) / Microsoft Docs
description: Fornece instruções para ligar openCensus Python com monitor Azure
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537113"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurar o Monitor Azure para a sua aplicação Python

O Azure Monitor suporta rastreios distribuídos, recolha métrica e registo de aplicações Python através da integração com [o OpenCensus.](https://opencensus.io) Este artigo irá acompanhá-lo através do processo de criação do OpenCensus para Python e enviar os seus dados de monitorização para o Monitor Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação Python. Este artigo usa [Python 3.7.0,](https://www.python.org/downloads/)embora versões anteriores provavelmente funcionem com pequenas alterações.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Criar um recurso Deinsights de Aplicação no Monitor Azure

Primeiro, é preciso criar um recurso Application Insights no Monitor Azure, que gerará uma chave de instrumentação (ikey). O ikey é então usado para configurar o OpenCensus SDK para enviar dados de telemetria para o Monitor Azure.

1. Selecione **Criar um programador de recursos** > **Ferramentas** > **De aplicação Insights**.

   ![Adicionar um recurso de Insights de Aplicação](./media/opencensus-python/0001-create-resource.png)

1. Aparece uma caixa de configuração. Utilize a tabela seguinte para preencher os campos de entrada.

   | Definição        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor globalmente único | Nome que identifique a app que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para acolher dados de Insights de Aplicação |
   | **Localização** | E.U.A. Leste | Um local perto de si, ou perto de onde a sua aplicação está hospedada |

1. Selecione **Criar**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com OpenCensus Python SDK para Monitor Azure

Instale os exportadores OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Para obter uma lista completa de pacotes e integrações, consulte [pacotes OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> O `python -m pip install opencensus-ext-azure` comando assume que `PATH` você tem uma variável ambiental definida para a sua instalação Python. Se ainda não configuraste esta variável, tens de dar o caminho completo do diretório para onde está o seu Python executável. O resultado é um `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`comando como este: .

O SDK utiliza três exportadores do Monitor Azure para enviar diferentes tipos de telemetria para o Monitor Azure: vestígios, métricas e registos. Para obter mais informações sobre estes tipos de telemetria, consulte [a visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Utilize as seguintes instruções para enviar estes tipos de telemetria através dos três exportadores.

## <a name="telemetry-type-mappings"></a>Mapeamentos tipo telemetria

Aqui estão os exportadores que o OpenCensus fornece mapeados para os tipos de telemetria que você verá no Monitor Azure.

![Screenshot de mapeamento de tipos de telemetria de OpenCensus para Monitor Azure](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Rastreio

> [!NOTE]
> `Trace`no OpenCensus refere-se ao [rastreio distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Os `AzureExporter` `requests` envios e `dependency` telemetria para o Monitor Azure.

1. Primeiro, vamos gerar alguns dados de vestígios localmente. Em Python IDLE, ou no seu editor de eleição, introduza o seguinte código.

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

2. A execução do código irá incita-lo repetidamente a introduzir um valor. A cada entrada, o valor será impresso na casca, e o Módulo `SpanData`Python OpenCensus gerará uma peça correspondente de . O projeto OpenCensus define um [traço como uma árvore de vãos.](https://opencensus.io/core-concepts/tracing/)
    
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

3. Embora a entrada de valores seja útil para fins `SpanData` de demonstração, em última análise queremos emitir o Monitor Azure. Passe a sua cadeia de ligação diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou pode especificá-la numa variável ambiental . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

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

4. Agora, quando executas o guião python, ainda deves ser solicitado a introduzir valores, mas apenas o valor está a ser impresso na concha. O `SpanData` criado será enviado para o Monitor Azure. Pode encontrar os dados de `dependencies`extensão emitidos em . . Para mais detalhes sobre os pedidos de saída, consulte [as dependências](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)do OpenCensus Python .
Para mais detalhes sobre os pedidos de entrada, consulte [os pedidos](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)do OpenCensus Python.

#### <a name="sampling"></a>Amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlação de vestígios

Para obter detalhes sobre a correlação de telemetria nos dados dos seus vestígios, dê uma olhada na correlação de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter mais detalhes sobre como modificar a telemetria rastreada antes de ser enviada para o Monitor Azure, consulte processadores de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados métricos locais. Vamos criar uma métrica simples para rastrear o número de vezes que o utilizador pressiona Enter.

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
2. A execução do código irá incidá-lo repetidamente para pressionar Enter. Uma métrica é criada para rastrear o número de vezes que enter é pressionado. A cada entrada, o valor será incrementado e a informação métrica será exibida na consola. A informação inclui o valor atual e o carimbo de tempo atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora a entrada de valores seja útil para fins de demonstração, em última análise queremos emitir os dados métricos para o Azure Monitor. Passe a sua cadeia de ligação diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou pode especificá-la numa variável ambiental . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

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

4. O exportador enviará dados métricos para o Monitor Azure num intervalo fixo. O padrão é a cada 15 segundos. Estamos a seguir uma única métrica, por isso estes dados métricos, com qualquer valor e carimbo de tempo que contenha, serão enviados a cada intervalo. Pode encontrar os `customMetrics`dados em .

#### <a name="standard-metrics"></a>Métricas padrão

Por padrão, o exportador de métricas enviará um conjunto de métricas padrão para o Monitor Azure. Pode desativar `enable_standard_metrics` isto `False` colocando a bandeira no construtor do exportador de métricas.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Abaixo está uma lista de métricas padrão que são atualmente enviadas:

- Memória Disponível (bytes)
- Tempo do processador CPU (percentagem)
- Taxa de Pedido de Entrada (por segundo)
- Tempo médio de execução do pedido de entrada (milissegundos)
- Taxa de Pedido de Saída (por segundo)
- Utilização do Processo CPU (percentagem)
- Processo Private Bytes (bytes)

Deve ser capaz de ver `performanceCounters`estas métricas em . A taxa de pedido `customMetrics`de entrada seria inferior a . Para mais informações, consulte [os contadores](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)de desempenho .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter mais detalhes sobre como modificar a telemetria rastreada antes de ser enviada para o Monitor Azure, consulte processadores de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

### <a name="logs"></a>Registos

1. Primeiro, vamos gerar alguns dados de registo locais.

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

2.  O código irá continuar a pedir a inscrição de um valor. É emitida uma entrada de registo por cada valor introduzido.

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

3. Embora a entrada de valores seja útil para fins de demonstração, em última análise queremos emitir os dados de registo para o Azure Monitor. Passe a sua cadeia de ligação diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou pode especificá-la numa variável ambiental . Modifique o seu código a partir do passo anterior com base na seguinte amostra de código:

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

4. O exportador enviará dados de registo para o Monitor Azure. Pode encontrar os `traces`dados em . 

> [!NOTE]
> `traces`neste contexto não é `Tracing`o mesmo que . `traces`refere-se ao tipo de telemetria que verá no Monitor `AzureLogHandler`Azure ao utilizar o . `Tracing`refere-se a um conceito no OpenCensus e diz respeito ao [rastreio distribuído.](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)

5. Para formatar as suas `formatters` mensagens de registo, pode utilizar na [API](https://docs.python.org/3/library/logging.html#formatter-objects)de registo python incorporada .

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

6. Também pode adicionar propriedades personalizadas às suas mensagens de registo no argumento de palavra-chave *extra* utilizando o campo custom_dimensions. Estes aparecerão como pares de `customDimensions` valor-chave no Monitor Azure.
> [!NOTE]
> Para que esta funcionalidade funcione, é necessário passar um dicionário para o campo custom_dimensions. Se passar em argumentos de qualquer outro tipo, o madeireiro ignorá-los-á.

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

#### <a name="sending-exceptions"></a>Envio de exceções

OpenCensus Python não rastreia e `exception` envia telemetria automaticamente. São enviados `AzureLogHandler` através da utilização de exceções através da biblioteca de madeira Python. Pode adicionar propriedades personalizadas, tal como com a exploração madeireira normal.

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
Uma vez que deve registar exceções explicitamente, cabe ao utilizador registar exceções não tratadas. O OpenCensus não coloca restrições na forma como um utilizador quer fazê-lo, desde que registem explicitamente uma telemetria de exceção.

#### <a name="sampling"></a>Amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlação de registos

Para mais detalhes sobre como enriquecer os seus registos com dados de contexto de rastreio, consulte a integração de [logs](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter mais detalhes sobre como modificar a telemetria rastreada antes de ser enviada para o Monitor Azure, consulte processadores de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

## <a name="view-your-data-with-queries"></a>Veja os seus dados com consultas

Pode visualizar os dados de telemetria que foram enviados da sua aplicação através do separador **Logs (Analytics).**

![Screenshot do painel de visão geral com "Logs (Analytics)" selecionado numa caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **Ative:**

- Para a telemetria enviada com o exportador de vestígios do Monitor Azure, os pedidos de entrada aparecem em . `requests` Os pedidos de saída ou `dependencies`de processo aparecem em .
- Para a telemetria enviada com o exportador de métricas do Monitor Azure, as métricas enviadas aparecem em baixo `customMetrics`.
- Para a telemetria enviada com o exportador de `traces`registos do Monitor Azure, os registos aparecem em baixo . As exceções `exceptions`aparecem em .

Para obter informações mais detalhadas sobre como utilizar consultas e registos, consulte [os registos no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Exportadores de monitores azure no GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrações OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Aplicações de amostra seleções do Monitor Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passos seguintes

* [Rastreio de pedidos de entrada](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Rastreio de pedidos de saída](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa de aplicações](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas métricos](../../azure-monitor/app/alerts.md): Destete alertas para o avisar se uma métrica atravessar um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.
