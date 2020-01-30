---
title: Depuração e problemas de aprendizagem automática em Insights de Aplicação
titleSuffix: Azure Machine Learning
description: Adicione o registo aos seus gasodutos de pontuação de treinamento e lote e veja os resultados registados em Insights de Aplicação.
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776303"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Depuração e problemas de aprendizagem automática em Insights de Aplicação
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A biblioteca de pitões [OpenCensus](https://opencensus.io/quickstart/python/) pode ser usada para direcionar os registos para os Insights de Aplicação a partir dos seus scripts. Aagregação de troncos do gasoduto corre num só local permite-lhe construir consultas e diagnosticar problemas. A utilização de Insights de Aplicação permitirá rastrear os registos ao longo do tempo e comparar os registos de gasodutos através de percursos.

Ter os seus registos no local uma vez fornecerá um histórico de exceções e mensagens de erro. Uma vez que a Application Insights se integra com o Azure Alerts, também pode criar alertas com base em consultas de Insights de Aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Siga os passos para criar um espaço de trabalho [Azure Machine Learning](./how-to-manage-workspace.md) e [crie o seu primeiro pipeline](./how-to-create-your-first-pipeline.md)
* [Configurar o ambiente de desenvolvimento](./how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning.
* Instale o pacote [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) localmente:
  ```python
  pip install opencensus-ext-azure
  ```
* Criar uma instância de Insights de [Aplicação](../azure-monitor/app/opencensus-python.md) (este doc também contém informações sobre a obtenção da cadeia de ligação para o recurso)

## <a name="getting-started"></a>Introdução

Esta secção é uma introdução específica para a utilização do OpenCensus a partir de um pipeline Azure Machine Learning. Para um tutorial detalhado, consulte [Os Exportadores de Monitor OpenCensus Azure](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adicione um PythonScriptStep ao seu Pipeline Azure ML. Configure a sua [Configuração run com](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) a dependência do opencensus-ext-azure. Configure a variável ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`.

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

Crie um ficheiro denominado `sample_step.py`. Importar a classe AzureLogHandler para encaminhar registos para Insights de Aplicação. Também terá de importar a biblioteca Python Logging.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Em seguida, adicione o AzureLogHandler ao madeireiro python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Exploração madeireira com dimensões personalizadas
 
Por padrão, os registos encaminhados para A Aplicação Insights não terão contexto suficiente para rastrear a execução ou a experiência. Para tornar os registos acionáveis para diagnosticar questões, são necessários campos adicionais. 

Para adicionar estes campos, as Dimensões Personalizadas podem ser adicionadas para fornecer contexto a uma mensagem de registo. Um exemplo é quando alguém quer ver troncos em vários passos na mesma corrida de gasodutos.

As Dimensões Personalizadas compõem um dicionário de pares de valor-chave (armazenados como cordas, cordas). O dicionário é então enviado para Application Insights e apresentado como uma coluna nos resultados da consulta. As suas dimensões individuais podem ser utilizadas como parâmetros de [consulta.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Contexto útil para incluir

| Campo                          | Raciocínio/Exemplo                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Pode consultar registos para aqueles com o mesmo parent_run_id ver registos ao longo do tempo para todos os passos, em vez de ter que mergulhar em cada passo individual                                        |
| step_id                        | Pode consultar registos para aqueles com o mesmo step_id para ver onde ocorreu um problema com um âmbito estreito para apenas o passo individual                                                        |
| step_name                      | Pode consultar os registos para ver o desempenho do passo ao longo do tempo. Também ajuda a encontrar uma step_id para corridas recentes sem mergulhar no portal UI                                          |
| experiment_name                | Pode consultar os registos para ver o desempenho da experiência ao longo do tempo. Também ajuda a encontrar um parent_run_id ou step_id para corridas recentes sem mergulhar no portal UI                   |
| run_url                 | Pode fornecer uma ligação diretamente de volta para a corrida para a investigação. |

**Outros campos úteis**

Estes campos podem necessitar de instrumentação adicional de código, e não são fornecidos pelo contexto de execução.

| Campo                   | Raciocínio/Exemplo                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Se utilizar ci/CD para implantar, este campo pode correlacionar os registos com a versão de código que forneceu a lógica de passo e pipeline. Esta ligação pode ainda ajudar a diagnosticar problemas, ou identificar modelos com características específicas (valores de log/métrica) |
| run_type                       | Pode diferenciar entre diferentes tipos de modelos, ou treino vs. corridas de pontuação |

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
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Considerações de exploração madeireira OpenCensus Python

O OpenCensus AzureLogHandler é utilizado para direcionar os registos python para insights de aplicação. Como resultado, as nuances de registo python devem ser consideradas. Quando um madeireiro é criado, tem um nível de registo predefinido e mostrará registos superiores ou iguais a esse nível. Uma boa referência para a utilização de características de exploração de python é o [Livro de Receitas de Exploração Madeireira.](https://docs.python.org/3/howto/logging-cookbook.html)

A variável ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` é necessária para a biblioteca OpenCensus. Recomendamos que esta variável ambiental em vez de a transmitir como parâmetro de gasoduto para evitar passar em torno de cordas de ligação de texto simples.

## <a name="querying-logs-in-application-insights"></a>Consulta de registos em Insights de Aplicação

Os registos encaminhados para a Aplicação Insights aparecerão em "vestígios" ou "exceções". Certifique-se de ajustar a sua janela de tempo para incluir o seu pipeline.

![Resultado da consulta de insights de aplicação](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

O resultado em Insights de Aplicação mostrará a mensagem de registo e o nível, o caminho do ficheiro e o número da linha de código. Também mostrará quaisquer dimensões personalizadas incluídas. Nesta imagem, o dicionário customDimensions mostra os pares chave/valor da amostra de [código](#creating-a-custom-dimensions-dictionary)anterior .

### <a name="additional-helpful-queries"></a>Consultas úteis adicionais

Algumas das consultas abaixo utilizam 'customDimensions.Level'. Estes níveis de gravidade correspondem ao nível com que o registo Python foi originalmente enviado. Para obter informações adicionais sobre consultas, consulte consultas de registo do [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Caso de utilização                                                               | Consulta                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Resultados de registo para dimensão personalizada específica, por exemplo 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Resultados do registo de todos os treinos nos últimos 7 dias                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Registar resultados com erro de nível de gravidade dos últimos 7 dias              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Contagem dos resultados do registo com erro de nível de gravidade nos últimos 7 dias     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Próximos Passos

Uma vez que tenha registos na sua instância Deinsights de Aplicação, podem ser usados para definir [alertas do Monitor Azure](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) com base em resultados de consulta.

Também pode adicionar resultados de consultas a um [Painel De Instrumentos Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) para obter informações adicionais.