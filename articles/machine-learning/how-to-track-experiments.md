---
title: Registar experimentações e métricas de ML
titleSuffix: Azure Machine Learning
description: Ativar o registo no seu treino de ML para monitorizar as métricas de execução em tempo real e para ajudar a diagnosticar erros e avisos.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d91c88da1416071b5eee2a8eb10e3029086839e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561785"
---
# <a name="enable-logging-in-ml-training-runs"></a>Permitir o registo em cursos de treino de ML


O SDK Python do Azure Machine Learning permite-lhe registar informações em tempo real com o pacote de registos Python predefinido e a funcionalidade específica do SDK. Pode registar localmente e enviar registos para a sua área de trabalho no portal.

Os registos podem ajudá-lo a diagnosticar erros e avisos ou a acompanhar métricas de desempenho, como parâmetros e desempenho do modelo. Neste artigo, vai aprender a ativar o registo nos seguintes cenários:

> [!div class="checklist"]
> * Sessões de preparação interativas
> * Enviar tarefas de preparação com ScriptRunConfig
> * Definições de `logging` nativas do Python
> * Registo de origens adicionais


> [!TIP]
> Este artigo mostra-lhe como monitorizar o processo de preparação do modelo. Se estiver interessado em monitorizar a utilização de recursos e os eventos do Azure Machine Learning, como quotas, execuções de preparação concluídas ou implementações de modelos concluídas, veja [Monitorizar o Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipos de dados

Pode registar vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens e diretórios, entre outros. Para obter mais informações e exemplos de código do Python para diferentes tipos de dados, veja a [página de referência da classe Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Métricas de execução de registo 

Utilize os seguintes métodos nas APIs de exploração madeireira para influenciar as visualizações de métricas. Note os [limites de serviço](./resource-limits-quotas-capacity.md#metrics) para estas métricas registadas. 

|Valor Registado|Código de exemplo| Formato no portal|
|----|----|----|
|Registar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de uma única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como de dentro de um loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Faça um registo de linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Tabela de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|
|Imagem de registo|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Utilize este método para registar um ficheiro de imagem ou um enredo de matplotlib para a execução. Estas imagens serão visíveis e comparáveis no registo de corridas|

### <a name="logging-with-mlflow"></a>Registo com MLflow
Utilize o MLFlowLogger para registar métricas.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Sessão de registo interativa

Normalmente, as sessões de registo interativas são utilizadas em ambientes de blocos de notas. O método [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) inicia uma sessão de registo interativa. Todas as métricas registadas durante a sessão são adicionadas ao registo de execução na experimentação. O método [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrun-logs"></a>Registos ScriptRun

Nesta secção, irá aprender a adicionar código de registo dentro de execuções criadas quando configuradas com ScriptRunConfig. Pode utilizar a classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) para encapsular os scripts e os ambientes para execuções repetíveis. Também pode utilizar esta opção para mostrar um widget de elemento visual de Jupyter Notebooks para monitorização.

Este exemplo realiza um varrimento de parâmetros em valores alfa e captura os resultados com o método [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Crie um script de preparação que inclua a lógica de registo, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Envie o script ```train.py``` a ser executado num ambiente gerido pelo utilizador. A pasta completa do script é enviada para preparação.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    O parâmetro `show_output` ativa o registo verboso, o que lhe permite ver detalhes do processo de preparação, bem como informações sobre quaisquer recursos remotos ou destinos de computação. Utilize o código seguinte para ativar o registo verboso ao enviar a experimentação.

```python
run = exp.submit(src, show_output=True)
```

Também pode utilizar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Registo do Python nativo

Alguns registos no SDK podem conter um erro que instrui o utilizador a definir o nível de registo como DEPURAÇÃO. Para definir o nível de registo, adicione o código seguinte ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Origens de registo adicionais

O Azure Machine Learning também pode registar informações de outras origens durante a preparação, como execuções de machine learning automatizado ou contentores do Docker que executam as tarefas. Estes registos não estão documentados, mas se encontrar problemas e entrar em contacto com o suporte da Microsoft, este pode utilizá-los durante a resolução de problemas.

Para obter informações sobre as métricas de registo no estruturador do Azure Machine Learning, veja [Como registar métricas no estruturador](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os blocos de notas seguintes demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Veja estes artigos para saber mais sobre como utilizar o Azure Machine Learning:

* Saiba como [registar métricas no estruturador do Azure Machine Learning](how-to-track-designer-experiments.md).

* Veja um exemplo sobre como registar o melhor modelo e implementá-lo no tutorial [Preparar um modelo de classificação de imagens com o Azure Machine Learning](tutorial-train-models-with-aml.md).