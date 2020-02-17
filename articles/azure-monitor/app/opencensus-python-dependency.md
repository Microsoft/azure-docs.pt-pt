---
title: Rastreio de dependência em Insights de Aplicação Azure com OpenCensus Python / Microsoft Docs
description: Monitorize as chamadas de dependência para as suas aplicações Python via OpenCensus Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 6217798f8175e7ecc1c1ec4068d7765444e4d2a2
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368286"
---
# <a name="track-dependencies-with-opencensus-python"></a>Dependências de pista com OpenCensus Python

Uma dependência é um componente externo que é chamado pela sua aplicação. Os dados de dependência são recolhidos usando o OpenCensus Python e as suas várias integrações. Os dados são então enviados para Application Insights no âmbito do Monitor Azure como `dependencies` telemetria.

Primeiro, instrumente a sua aplicação Python com o mais recente [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dependências em processo

OpenCensus Python SDK para O Monitor Azure permite-lhe enviar telemetria de dependência "em processo" (informação e lógica que ocorre dentro da sua aplicação). As dependências em processo terão o campo `type` como `INPROC` na análise.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com integração de "pedidos"

Acompanhe os seus pedidos de saída com o OpenCensus `requests` integração.

Descarregue e instale `opencensus-ext-requests` a partir do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de vestígios. Os pedidos enviados através da biblioteca [de pedidos](https://pypi.org/project/requests/) python serão rastreados.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dependências com integração "httplib"

Acompanhe os seus pedidos de saída com o OpenCensus `httplib` integração.

Descarregue e instale `opencensus-ext-httplib` a partir do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de vestígios. Os pedidos enviados usando [http.client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) para Python2 serão rastreados.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dependências com integração "django"

Acompanhe os seus pedidos de Django cessantes com o OpenCensus `django` integração.

Descarregue e instale `opencensus-ext-django` a partir do [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à secção `MIDDLEWARE` no ficheiro Django `settings.py`.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Configuração adicional pode ser fornecida, leia [personalizações](https://github.com/census-instrumentation/opencensus-python#customization) para uma referência completa.

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

## <a name="dependencies-with-mysql-integration"></a>Dependências com integração "mysql"

Acompanhe as suas dependências MYSQL com o OpenCensus `mysql` integração. Esta integração suporta a biblioteca [de conector misépito.](https://pypi.org/project/mysql-connector-python/)

Descarregue e instale `opencensus-ext-mysql` a partir do [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com integração "pymysql"

Acompanhe as dependências do PyMySQL com o OpenCensus `pymysql` integração.

Descarregue e instale `opencensus-ext-pymysql` a partir do [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com integração "pós-gresq"

Acompanhe as dependências do PostgreSQL com o OpenCensus `postgresql` integração. Esta integração apoia a biblioteca [psycopg2.](https://pypi.org/project/psycopg2/)

Descarregue e instale `opencensus-ext-postgresql` a partir do [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com integração "pymongo"

Acompanhe as suas dependências do MongoDB com o OpenCensus `pymongo` integração. Esta integração apoia a biblioteca [pymongo.](https://pypi.org/project/pymongo/)

Descarregue e instale `opencensus-ext-pymongo` a partir do [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com integração de "sqlalchemy"

Acompanhe as suas dependências utilizando o SQLAlchemy utilizando o OpenCensus `sqlalchemy` integração. Esta integração acompanha a utilização do pacote [de sqlalchemy,](https://pypi.org/project/SQLAlchemy/) independentemente da base de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Passos seguintes

* [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnósticos de transações](../../azure-monitor/app/transaction-diagnostics.md)
