---
title: Monitorizar aplicações Python com o Azure Application Insights | Documentos da Microsoft
description: Fornece instruções para conectar OpenCensus Python com o Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541443"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Recolher rastreios distribuídos do Python (pré-visualização)

O Application Insights agora suporta distribuídas de aplicativos do Python através da integração com o rastreamento [OpenCensus](https://opencensus.io). Este artigo irá guiá-lo passo a passo pelo processo de configuração OpenCensus para Python e obter os seus dados de monitorização para o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma Subscrição do Azure.
- Deve ser instalado o Python, este artigo usa [Python 3.7.0](https://www.python.org/downloads/), embora as versões anteriores irão provável que funcionem com o pequeno ajuste.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso do Application Insights

Primeiro tem de criar um recurso do Application Insights, que gerará um key(ikey) de instrumentação. A ikey, em seguida, é utilizada para configurar o SDK de OpenCensus para enviar dados de telemetria para o Application Insights.

1. Selecione **criar um recurso** > **ferramentas de programação** > **Application Insights**.

   ![Adicionar um Recurso do Application Insights](./media/opencensus-python/0001-create-resource.png)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        | Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Location** | East US | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Instalar OpenCensus Azure Monitor Exporters

1. Instale os Exporters de Monitor do OpenCensus Azure:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` pressupõe-se de que tem uma variável de ambiente de caminho definida para a instalação do Python. Se não tiver configurado a isso, seria necessário fornecer o caminho completo do diretório para onde o Python executável está localizado que teria resultado num comando como: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Primeiro vamos gerar alguns dados de rastreio localmente. Na INATIVIDADE de Python ou no seu editor preferencial, introduza o seguinte código:

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

3. A execução do código repetidamente solicitará que insira um valor. A cada entrada, o valor serão impressos para o shell e uma parte correspondente do **SpanData** serão gerados pelo módulo de Python OpenCensus. O projeto de OpenCensus define um [ _rastreio como uma árvore de spans_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Embora seja útil para fins de demonstração, em última análise, queremos emitir o SpanData para o Application Insights. Modifique o código do passo anterior com base no exemplo de código seguinte:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Agora, quando executar o script de Python, deve ainda ser-lhe pedido para introduzir os valores, mas agora apenas o valor está a ser impresso no shell.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir o Application Insights **descrição geral** página no portal do Azure, para ver os detalhes sobre a sua aplicação em execução. Selecione **Live Stream métrica**.

   ![Captura de ecrã do painel de descrição geral com o fluxo de métricas em direto selecionado na caixa vermelha](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Navegue de volta para o **descrição geral** página e selecione **mapa da aplicação** para um esquema visual das relações de dependência e chamada de tempo entre os componentes da aplicação.

    ![Captura de ecrã do mapa de aplicativo básico](./media/opencensus-python/0007-application-map.png)

    Uma vez que estamos apenas foram rastrear uma chamada de método, nosso mapa da aplicação não é tão interessante. Mas o mapa da aplicação pode ser dimensionado para visualizar as aplicações distribuídas muito mais:

   ![Mapeamento de Aplicações](media/opencensus-python/application-map.png)

3. Selecione **investigar desempenho** para efetuar a análise detalhada do desempenho e determinar a causa de raiz de um desempenho lento.

    ![Captura de ecrã do painel de desempenho](./media/opencensus-python/0008-performance.png)

4. Selecionando **amostras** e, em seguida, clicar em qualquer um dos exemplos que são apresentados no painel da direita iniciará a experiência de detalhes de transação de ponta a ponta. Embora a nossa aplicação de exemplo irá mostrar-num único evento, uma aplicação mais complexa permitiria que explore a transação de ponta a ponta para baixo até o nível de pilha de chamadas de um evento individual.

     ![Captura de ecrã da interface de transação de ponta a ponta](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus para Python

* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integração de flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integração do Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integração do MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Passos Seguintes

* [Python OpenCensus no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Mapa da aplicação](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponto a ponto](./../../azure-monitor/learn/tutorial-performance.md)