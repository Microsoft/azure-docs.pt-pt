---
title: Rastreio de dependência em Insights de Aplicação Azure com | De Pitão OpenCensus Microsoft Docs
description: Monitora chamadas de dependência para as suas aplicações Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585713"
---
# <a name="track-dependencies-with-opencensus-python"></a>Dependências de pistas com OpenCensus Python

Uma dependência é um componente externo chamado pela aplicação. Os dados de dependência são recolhidos usando o OpenCensus Python e as suas várias integrações. Os dados são então enviados para a Application Insights no Azure Monitor como `dependencies` telemetria.

Em primeiro lugar, instrumento a sua aplicação Python com o mais recente [OpenCensus Python SDK](./opencensus-python.md).

## <a name="in-process-dependencies"></a>Dependências em processo

O OpenCensus Python SDK para o Azure Monitor permite-lhe enviar telemetria de dependência "in-process" (informação e lógica que ocorra dentro da sua aplicação). As dependências em processo terão o `type` campo como `INPROC` na análise.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com integração de "pedidos"

Acompanhe os seus pedidos de saída com a integração OpenCensus. `requests`

Faça o download e instale `opencensus-ext-requests` a partir do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de vestígios. Os pedidos enviados através da biblioteca de [pedidos](https://pypi.org/project/requests/) python serão rastreados.

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

Acompanhe os seus pedidos de saída com a integração do OpenCensus. `httplib`

Faça o download e instale `opencensus-ext-httplib` a partir do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de vestígios. Os pedidos enviados através do [http.client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) para Python2 serão rastreados.

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

Acompanhe os seus pedidos de Django com a integração OpenCensus. `django`

> [!NOTE]
> Os únicos pedidos de Django que são rastreados são as chamadas feitas para uma base de dados. Para pedidos feitos ao pedido de Django, consulte [os pedidos de entrada.](./opencensus-python-request.md#tracking-django-applications)

Faça o download e instale `opencensus-ext-django` a partir de [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à `MIDDLEWARE` secção no ficheiro `settings.py` Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Pode ser fornecida configuração adicional, ler [personalizações](https://github.com/census-instrumentation/opencensus-python#customization) para uma referência completa.

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

Acompanhe as suas dependências MYSQL com a integração OpenCensus. `mysql` Esta integração suporta a biblioteca [de conector mysql.](https://pypi.org/project/mysql-connector-python/)

Faça o download e instale `opencensus-ext-mysql` a partir de [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com integração "pymysql"

Acompanhe as suas dependências PyMySQL com a integração OpenCensus. `pymysql`

Faça o download e instale `opencensus-ext-pymysql` a partir de [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com integração "postgresql"

Acompanhe as suas dependências postgreSQL com a integração OpenCensus. `postgresql` Esta integração suporta a biblioteca [psycopg2.](https://pypi.org/project/psycopg2/)

Faça o download e instale `opencensus-ext-postgresql` a partir de [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com integração "pymongo"

Acompanhe as suas dependências mongoDB com a integração OpenCensus. `pymongo` Esta integração apoia a biblioteca [do Pymongo.](https://pypi.org/project/pymongo/)

Faça o download e instale `opencensus-ext-pymongo` a partir de [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com integração "sqlalchemy"

Acompanhe as suas dependências utilizando a SQLAlchemy utilizando a integração OpenCensus. `sqlalchemy` Esta integração acompanha o uso do pacote [sqlalchemy,](https://pypi.org/project/SQLAlchemy/) independentemente da base de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Passos seguintes

* [Mapeamento de Aplicações](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisa](./diagnostic-search.md)
* [Consulta de log (Analytics)](../logs/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

