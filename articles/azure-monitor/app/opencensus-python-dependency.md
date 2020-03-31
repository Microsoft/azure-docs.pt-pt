---
title: Rastreio de dependência em Insights de Aplicação Azure com OpenCensus Python / Microsoft Docs
description: Monitorize as chamadas de dependência para as suas aplicações Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669935"
---
# <a name="track-dependencies-with-opencensus-python"></a>Dependências de pista com OpenCensus Python

Uma dependência é um componente externo que é chamado pela sua aplicação. Os dados de dependência são recolhidos usando o OpenCensus Python e as suas várias integrações. Os dados são então enviados para `dependencies` Application Insights no âmbito do Monitor Azure como telemetria.

Primeiro, instrumente a sua aplicação Python com o mais recente [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dependências em processo

OpenCensus Python SDK para O Monitor Azure permite-lhe enviar telemetria de dependência "em processo" (informação e lógica que ocorre dentro da sua aplicação). As dependências em processo `type` terão o campo como `INPROC` na análise.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com integração de "pedidos"

Acompanhe os seus pedidos `requests` de saída com a integração OpenCensus.

Descarregue `opencensus-ext-requests` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de vestígios. Os pedidos enviados através da biblioteca [de pedidos](https://pypi.org/project/requests/) python serão rastreados.

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

Acompanhe os seus pedidos `httplib` de saída com integração OpenCensus.

Descarregue `opencensus-ext-httplib` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de vestígios. Os pedidos enviados usando [http.client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) para Python2 serão rastreados.

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

Acompanhe os seus pedidos django `django` cessantes com a integração OpenCensus.

Descarregue `opencensus-ext-django` e instale a partir `MIDDLEWARE` do [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à secção do ficheiro Django. `settings.py`

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

Acompanhe as suas dependências MYSQL com a integração OpenCensus. `mysql` Esta integração suporta a biblioteca [de conector misépito.](https://pypi.org/project/mysql-connector-python/)

Descarregue `opencensus-ext-mysql` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com integração "pymysql"

Acompanhe as dependências do PyMySQL com a integração OpenCensus. `pymysql`

Descarregue `opencensus-ext-pymysql` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com integração "pós-gresq"

Acompanhe as dependências do PostgreSQL com a integração OpenCensus. `postgresql` Esta integração apoia a biblioteca [psycopg2.](https://pypi.org/project/psycopg2/)

Descarregue `opencensus-ext-postgresql` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com integração "pymongo"

Acompanhe as suas dependências do `pymongo` MongoDB com a integração OpenCensus. Esta integração apoia a biblioteca [pymongo.](https://pypi.org/project/pymongo/)

Descarregue `opencensus-ext-pymongo` e instale a partir do [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com integração de "sqlalchemy"

Acompanhe as suas dependências utilizando a `sqlalchemy` SQLAlchemy utilizando a integração OpenCensus. Esta integração acompanha a utilização do pacote [de sqlalchemy,](https://pypi.org/project/SQLAlchemy/) independentemente da base de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Passos seguintes

* [Mapa de aplicações](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Procurar](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnósticos de transações](../../azure-monitor/app/transaction-diagnostics.md)
