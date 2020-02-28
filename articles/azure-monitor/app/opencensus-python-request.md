---
title: Acompanhamento de pedidos de entrada em Insights de aplicação Azure com OpenCensus Python  Microsoft Docs
description: Monitorize chamadas de pedidos para as suas aplicações Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669952"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhe os pedidos de entrada com openCensus Python

Os dados de pedidos de entrada são recolhidos utilizando o OpenCensus Python e as suas várias integrações. Acompanhe os dados de pedidos enviados para as suas aplicações web construídas em cima dos quadros web populares `django`, `flask` e `pyramid`. Os dados são então enviados para Application Insights no âmbito do Monitor Azure como `requests` telemetria.

Primeiro, instrumente a sua aplicação Python com o mais recente [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Rastreio de aplicações Django

1. Descarregue e instale `opencensus-ext-django` a partir do [PyPI](https://pypi.org/project/opencensus-ext-django/) e ateste a sua aplicação com o `django` middleware. Os pedidos de entrada enviados para o seu pedido de `django` serão rastreados.

2. Inclua `opencensus.ext.django.middleware.OpencensusMiddleware` no seu ficheiro `settings.py` sob `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Certifique-se de que o AzureExporter está corretamente configurado no seu `settings.py` em `OPENCENSUS`.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. Também pode adicionar urls a `settings.py` sob `BLACKLIST_PATHS` para pedidos que não quer rastrear.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Aplicações de frasco de rastreio

1. Descarregue e instale `opencensus-ext-flask` a partir do [PyPI](https://pypi.org/project/opencensus-ext-flask/) e ateste a sua aplicação com o `flask` middleware. Os pedidos de entrada enviados para o seu pedido de `flask` serão rastreados.

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

2. Pode configurar o seu `flask` middleware diretamente no código. Para pedidos de urls que não deseje rastrear, adicione-os a `BLACKLIST_PATHS`.

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

## <a name="tracking-pyramid-applications"></a>Aplicações de Pirâmide de Rastreio

1. Descarregue e instale `opencensus-ext-django` a partir do [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e ateste a sua aplicação com a `pyramid` interpolação. Os pedidos de entrada enviados para o seu pedido de `pyramid` serão rastreados.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Pode configurar o seu `pyramid` interpolação diretamente no código. Para pedidos de urls que não deseje rastrear, adicione-os a `BLACKLIST_PATHS`.

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

* [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnósticos de transações](../../azure-monitor/app/transaction-diagnostics.md)
