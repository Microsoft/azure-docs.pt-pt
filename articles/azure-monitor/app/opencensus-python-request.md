---
title: Rastreio de pedido de entrada em Azure Application Insights com OpenCensus Python / Microsoft Docs
description: Monitor solicita chamadas para as suas aplicações Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850071"
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

3. Certifique-se de que o AzureExporter está corretamente configurado na sua `settings.py` `OPENCENSUS` sub. Para pedidos de urls que não deseja rastrear, adicione-os a `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Também pode configurar a sua `flask` aplicação através de `app.config` . Para pedidos de urls que não deseja rastrear, adicione-os a `BLACKLIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Pode configurar a sua `pyramid` interpolação diretamente no código. Para pedidos de urls que não deseja rastrear, adicione-os a `BLACKLIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Mapeamento de Aplicações](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisa](./diagnostic-search.md)
* [Consulta de log (Analytics)](../log-query/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

