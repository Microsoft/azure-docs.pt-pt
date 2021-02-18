---
title: Monitor & recolher ficheiros de registo de gasodutos
titleSuffix: Azure Machine Learning
description: Adicione o registo de login aos seus pipelines de treinamento e pontuação de lote e veja os resultados registados em Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 58e604eccaca4630a235f4ae83724df20d6b1e26
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592526"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Recolha ficheiros de registo de gasodutos de aprendizagem automática em Application Insights para alertas e depurações


A biblioteca [de pitões OpenCensus](https://opencensus.io/quickstart/python/) pode ser usada para encaminhar registos para Insights de Aplicação a partir dos seus scripts. A agregação de troncos de gasodutos funciona num só local, permite-lhe construir consultas e diagnosticar problemas. A utilização de Insights de Aplicação permitir-lhe-á rastrear registos ao longo do tempo e comparar registos de gasodutos em execuções.

Ter os seus logins uma vez no local irá fornecer um histórico de exceções e mensagens de erro. Uma vez que o Application Insights se integra com os Alertas Azure, também pode criar alertas baseados em consultas de Insights de Aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Siga os passos para criar um espaço de trabalho [de Aprendizagem de Máquinas Azure](./how-to-manage-workspace.md) e [crie o seu primeiro pipeline](./how-to-create-machine-learning-pipelines.md)
* [Configure o seu ambiente de desenvolvimento](./how-to-configure-environment.md) para instalar o Azure Machine Learning SDK.
* Instale o pacote [de exportador do Monitor OpenCensus Azure](https://pypi.org/project/opencensus-ext-azure/) localmente:
  ```python
  pip install opencensus-ext-azure
  ```
* Crie uma [instância de Insights de Aplicação](../azure-monitor/app/opencensus-python.md) (este doc também contém informações sobre a obtenção da cadeia de ligação para o recurso)

## <a name="getting-started"></a>Introdução

Esta secção é uma introdução específica para a utilização do OpenCensus a partir de um oleoduto de aprendizagem automática Azure. Para um tutorial detalhado, consulte os [Exportadores do Monitor OpenCensus Azure](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adicione um PythonScriptStep ao seu Pipeline Azure ML. Configure a sua [Configuração run](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) com a dependência do opencensus-ext-azure. Configure a `APPLICATIONINSIGHTS_CONNECTION_STRING` variável ambiental.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Crie um ficheiro denominado `sample_step.py`. Importe a classe AzureLogHandler para encaminhar registos para Application Insights. Também terá de importar a biblioteca python logging.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Em seguida, adicione o AzureLogHandler ao madeirão de pitão.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Registo com Dimensões Personalizadas
 
Por predefinição, os registos reencaminhados para o Application Insights não terão contexto suficiente para rastrear até à execução ou experiência. Para tornar os registos accuíveis para o diagnóstico de problemas, são necessários campos adicionais. 

Para adicionar estes campos, as Dimensões Personalizadas podem ser adicionadas para fornecer contexto a uma mensagem de registo. Um exemplo é quando alguém quer ver troncos em vários degraus no mesmo oleoduto.

As Dimensões Personalizadas compõem um dicionário de pares de valor-chave (armazenados como cordas, cordas). O dicionário é então enviado para Insights de Aplicação e apresentado como uma coluna nos resultados da consulta. As suas dimensões individuais podem ser utilizadas como [parâmetros de consulta.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Contexto útil para incluir

| Campo                          | Raciocínio/Exemplo                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Pode consultar registos para aqueles com a mesma parent_run_id ver registos ao longo do tempo para todos os passos, em vez de ter que mergulhar em cada passo individual                                        |
| step_id                        | Pode consultar registos para aqueles com a mesma step_id ver onde um problema ocorreu com um âmbito restrito apenas para o passo individual                                                        |
| step_name                      | Pode consultar registos para ver o desempenho do passo ao longo do tempo. Também ajuda a encontrar um step_id para corridas recentes sem mergulhar no portal UI                                          |
| experiment_name                | Pode consultar os registos para ver o desempenho da experiência ao longo do tempo. Também ajuda a encontrar um parent_run_id ou step_id para corridas recentes sem mergulhar no portal UI                   |
| run_url                 | Pode fornecer uma ligação diretamente de volta para a corrida para a investigação. |

**Outros campos úteis**

Estes campos podem requerer uma instrumentação adicional de código, e não são fornecidos pelo contexto de execução.

| Campo                   | Raciocínio/Exemplo                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se utilizar o CI/CD para implementar, este campo pode correlacionar os registos com a versão de código que forneceu a lógica do passo e do gasoduto. Esta ligação pode ajudar ainda mais a diagnosticar problemas, ou identificar modelos com características específicas (valores de log/métrica) |
| run_type                       | Pode diferenciar diferentes tipos de modelos, ou treino vs. corridas de pontuação |

### <a name="creating-a-custom-dimensions-dictionary"></a>Criação de um dicionário de dimensões personalizadas

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>Considerações de registo de madeira openCensus Python

O OpenCensus AzureLogHandler é utilizado para encaminhar os registos python para Insights de Aplicação. Como resultado, as nuances de registo python devem ser consideradas. Quando um madeireiro é criado, tem um nível de registo predefinido e mostrará registos maiores ou iguais a esse nível. Uma boa referência para a utilização de funcionalidades de registo python é o [Livro de Receitas de Registo.](https://docs.python.org/3/howto/logging-cookbook.html)

A `APPLICATIONINSIGHTS_CONNECTION_STRING` variável ambiental é necessária para a biblioteca OpenCensus. Recomendamos a definição desta variável ambiente em vez de a passar como parâmetro de gasoduto para evitar passar em torno de cordas de ligação de texto simples.

## <a name="querying-logs-in-application-insights"></a>Consulta de registos em Insights de Aplicação

Os registos encaminhados para Application Insights aparecerão em "vestígios" ou "exceções". Certifique-se de ajustar a janela de tempo para incluir o seu curso de gasoduto.

![Resultado da consulta de insights de aplicação](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

O resultado em Application Insights mostrará a mensagem de registo e o nível, a trajetória do ficheiro e o número da linha de código. Também mostrará quaisquer dimensões personalizadas incluídas. Nesta imagem, o dicionário customDimensions mostra os pares chave/valor da amostra de [código](#creating-a-custom-dimensions-dictionary)anterior.

### <a name="additional-helpful-queries"></a>Consultas adicionais úteis

Algumas das consultas abaixo utilizam 'customDimensions.Level'. Estes níveis de gravidade correspondem ao nível com que o tronco python foi originalmente enviado. Para obter informações adicionais sobre consultas, consulte [as consultas de registo do Monitor de Azure](/azure/data-explorer/kusto/query/).

| Caso de utilização                                                               | Consulta                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Registar resultados para uma dimensão personalizada específica, por exemplo, 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Registar resultados para todos os treinos nos últimos 7 dias                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Registar resultados com severidade Erro de nível dos últimos 7 dias              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Contagem de resultados de registo com severidade Erro de nível nos últimos 7 dias     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Passos Seguintes

Uma vez que tenha registos na sua instância De Insights de Aplicação, podem ser usados para definir [alertas do Azure Monitor](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) com base nos resultados da consulta.

Também pode adicionar resultados de consultas a um [Painel Azure](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) para obter informações adicionais.