---
title: Monitorar aplicativos Python com o Azure Monitor | Microsoft Docs
description: Fornece instruções para conectar o Python OpenCensus com o Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293992"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurar Azure Monitor para seu aplicativo Python

O Azure Monitor dá suporte ao rastreamento distribuído, à coleta de métrica e ao log de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io). Este artigo orientará você passo a passo pelo processo de configuração do OpenCensus para Python e pela obtenção dos dados de monitoramento para Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma Subscrição do Azure.
- O Python deve ser instalado, este artigo usa o [Python 3.7.0](https://www.python.org/downloads/), embora as versões anteriores funcionem com pequenos ajustes.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Criar Application Insights recurso no Azure Monitor

Primeiro, você precisa criar um recurso de Application Insights no Azure Monitor, que irá gerar uma chave de instrumentação (iKey). Em seguida, o iKey é usado para configurar o SDK do OpenCensus para enviar dados de telemetria para Azure Monitor.

1. Selecione **criar um recurso** > **ferramentas para desenvolvedores** > **Application insights**.

   ![Adicionar um Recurso do Application Insights](./media/opencensus-python/0001-create-resource.png)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Localização** | Este dos E.U.A. | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentação com o SDK do OpenCensus Python para Azure Monitor

1. Instale o OpenCensus Azure Monitor exportadores:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente PATH definida para a instalação do Python. Se você não tiver configurado isso, precisará fornecer o caminho completo do diretório para onde o executável do Python está localizado, o que resultaria em um comando como: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. O SDK utiliza três Azure Monitor exportadores para enviar tipos diferentes de telemetria para Azure Monitor: rastreamento, métricas e logs. Veja [a visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) para obter mais detalhes sobre esses tipos diferentes. Siga as instruções abaixo para ver como enviar esses tipos diferentes por meio dos três exportadores.

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

2. A execução do código solicitará repetidamente que você insira um valor. Com cada entrada, o valor será impresso no Shell e uma parte correspondente de **SpanData** será gerada pelo módulo python OpenCensus. O projeto OpenCensus define um [_rastreamento como uma árvore de Spans_](https://opencensus.io/core-concepts/tracing/).
    
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

3. Embora seja útil para fins de demonstração, por fim, desejamos emitir o `SpanData` para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Agora, ao executar o script Python, você ainda deve ser solicitado a inserir valores, mas agora apenas o valor está sendo impresso no Shell. O `SpanData` criado será enviado para Azure Monitor. Você pode encontrar os dados de span emitidos em `dependencies`.

### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados de métrica local. Criaremos uma métrica simples para acompanhar o número de vezes que o usuário pressiona ENTER.

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
2. A execução do código solicitará repetidamente que você pressione Enter. Uma métrica é criada para acompanhar o número de inserções pressionadas. Com cada entrada, o valor será incrementado e as informações da métrica serão exibidas no console, com o valor atual e o carimbo de data/hora atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora seja útil para fins de demonstração, por fim, desejamos emitir os dados de métrica para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

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
    )
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

4. O exportador enviará dados de métrica para Azure Monitor em um intervalo fixo, o padrão será a cada 15 segundos. Estamos acompanhando uma única métrica para que esses dados de métrica, com qualquer valor e carimbo de data/hora que ele contenha, serão enviados a cada intervalo. Você pode encontrar os dados em `customMetrics`.

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

2.  O código solicitará continuamente um valor a ser inserido. Uma entrada de log é emitida para cada valor inserido contendo o valor dito.

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

3. Embora seja útil para fins de demonstração, por fim, desejamos emitir os dados de métrica para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

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

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora você pode reabrir a página de **visão geral** Application Insights no portal do Azure, para exibir detalhes sobre o aplicativo em execução no momento. Selecione **fluxo de métrica ao vivo**.

   ![Captura de tela do painel Visão geral com o Live Metric Stream selecionado na caixa vermelha](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Navegue de volta para a página **visão geral** e selecione **mapa do aplicativo** para obter um layout visual das relações de dependência e o tempo de chamada entre os componentes do aplicativo.

    ![Captura de tela do mapa do aplicativo básico](./media/opencensus-python/0007-application-map.png)

    Como estamos rastreando apenas uma chamada de método, nosso mapa de aplicativos não é tão interessante. Mas o mapa do aplicativo pode ser dimensionado para visualizar muito mais aplicativos distribuídos:

   ![Mapeamento de Aplicações](media/opencensus-python/application-map.png)

3. Selecione **investigar desempenho** para executar análise de desempenho detalhada e determinar a causa raiz do desempenho lento.

    ![Captura de tela do painel de desempenho](./media/opencensus-python/0008-performance.png)

4. Selecionar **exemplos** e clicar em qualquer um dos exemplos que aparecem no painel à direita iniciará a experiência de detalhes da transação de ponta a ponta. Embora nosso aplicativo de exemplo apenas mostre-nos um único evento, um aplicativo mais complexo permitiria que você explorasse a transação de ponta a ponta até o nível da pilha de chamadas de um evento individual.

     ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Exibir seus dados com consultas

1. Você pode exibir os dados de telemetria que foram enviados do seu aplicativo por meio da guia logs (análise).

    ![Captura de tela do painel Visão geral com logs (análise) selecionados na caixa vermelha](./media/opencensus-python/0010-logs-query.png)

2. Para a telemetria enviada com o exportador de rastreamento de Azure Monitor, as solicitações de entrada aparecerão em `requests` e as solicitações de processo de saída/saída serão exibidas em `dependencies`.

3. Para a telemetria enviada com a Azure Monitor o exportador de métricas, as métricas enviadas serão exibidas em `customMetrics`.

4. Para a telemetria enviada com o exportador de logs de Azure Monitor, os logs serão exibidos em `traces` e as exceções serão exibidas em `exceptions`.

5. Dê uma olhada nos [logs em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) para obter informações mais detalhadas sobre como usar consultas e logs.

## <a name="opencensus-for-python"></a>OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Customization](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integração do Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integração do Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integração do MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passos seguintes

* [Resumo da API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas de métrica](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica ultrapassar um limite. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.