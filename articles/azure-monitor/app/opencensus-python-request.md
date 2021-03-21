---
title: Rastreio de pedido de entrada em insights de aplicação Azure com | De Pitão OpenCensus Microsoft Docs
description: Monitor solicita chamadas para as suas aplicações Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585680"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhe os pedidos de entrada com o OpenCensus Python

Os dados de pedido de entrada são recolhidos utilizando o OpenCensus Python e as suas várias integrações. Acompanhe os dados de pedido de entrada enviados para as suas aplicações web construídas em cima das populares estruturas web `django` , `flask` e `pyramid` . Os dados são então enviados para a Application Insights no Azure Monitor como `requests` telemetria.

Em primeiro lugar, instrumento a sua aplicação Python com o mais recente [OpenCensus Python SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Rastreio de aplicações de Django

1. Faça o download e instale `opencensus-ext-django` a partir de [PyPI](https://pypi.org/project/opencensus-ext-django/) e instrumento a sua aplicação com o `django` middleware. Os pedidos de entrada enviados para a sua `django` candidatura serão rastreados.

2. Incluir `opencensus.ext.django.middleware.OpencensusMiddleware` no seu ficheiro em `settings.py` `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Certifique-se de que o AzureExporter está corretamente configurado na sua `settings.py` `OPENCENSUS` sub. Para pedidos de urls que não deseja rastrear, adicione-os a `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Aplicações de rastreio de frascos

1. Faça o download e instale `opencensus-ext-flask` a partir de [PyPI](https://pypi.org/project/opencensus-ext-flask/) e instrumento a sua aplicação com o `flask` middleware. Os pedidos de entrada enviados para a sua `flask` candidatura serão rastreados.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Também pode configurar a sua `flask` aplicação através de `app.config` . Para pedidos de urls que não deseja rastrear, adicione-os a `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Aplicações de pirâmide de rastreio

1. Faça o download e instale `opencensus-ext-django` a partir de [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e instrumento a sua aplicação com a `pyramid` interpolação. Os pedidos de entrada enviados para a sua `pyramid` candidatura serão rastreados.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Pode configurar a sua `pyramid` interpolação diretamente no código. Para pedidos de urls que não deseja rastrear, adicione-os a `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Rastreio de aplicações FastAPI

O OpenCensus não tem uma extensão para o FastAPI. Para escrever o seu próprio middleware FastAPI, complete os seguintes passos:

1. São necessárias as seguintes dependências: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Adicione [middleware FastAPI](https://fastapi.tiangolo.com/tutorial/middleware/). Certifique-se de que define o servidor tipo de extensão: `span.span_kind = SpanKind.SERVER` .

3. Executar a sua candidatura. As chamadas efetuadas para a sua aplicação FastAPI devem ser rastreadas automaticamente e a telemetria deve ser registada diretamente no Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Passos seguintes

* [Mapeamento de Aplicações](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisa](./diagnostic-search.md)
* [Consulta de log (Analytics)](../logs/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

