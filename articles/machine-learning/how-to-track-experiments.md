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
ms.openlocfilehash: 235d54d36965ac5d8d173354b2223247806a1fff
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803668"
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

Pode registar vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens e diretórios, entre outros. Para obter mais informações e exemplos de código do Python para diferentes tipos de dados, veja a [página de referência da classe Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessão de registo interativa

Normalmente, as sessões de registo interativas são utilizadas em ambientes de blocos de notas. O método [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) inicia uma sessão de registo interativa. Todas as métricas registadas durante a sessão são adicionadas ao registo de execução na experimentação. O método [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrun-logs"></a>Registos ScriptRun

Nesta secção, irá aprender a adicionar código de registo dentro de execuções criadas quando configuradas com ScriptRunConfig. Pode utilizar a classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para encapsular os scripts e os ambientes para execuções repetíveis. Também pode utilizar esta opção para mostrar um widget de elemento visual de Jupyter Notebooks para monitorização.

Este exemplo realiza um varrimento de parâmetros em valores alfa e captura os resultados com o método [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Crie um script de preparação que inclua a lógica de registo, `train.py`.

   [!code-python](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)


1. Envie o script ```train.py``` a ser executado num ambiente gerido pelo utilizador. A pasta completa do script é enviada para preparação.

   [!notebook-python](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src) 
    [!notebook-python](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)

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

[! INCLUIR aml-clone-in-azure-notebook](https://github.com/MicrosoftDocs/azure-docs-pr/blob/live/includes/aml-clone-for-examples.md)

## <a name="next-steps"></a>Passos seguintes

Veja estes artigos para saber mais sobre como utilizar o Azure Machine Learning:

* Saiba como [registar métricas no estruturador do Azure Machine Learning](how-to-track-designer-experiments.md).

* Veja um exemplo sobre como registar o melhor modelo e implementá-lo no tutorial [Preparar um modelo de classificação de imagens com o Azure Machine Learning](tutorial-train-models-with-aml.md).
