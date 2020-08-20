---
title: Experimente experiências de ML & métricas
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências Azure ML e monitorize as métricas de execução para melhorar o processo de criação de modelos. Adicione o registo no seu script de treino usando run.log, Run.start_logging ou ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f49a66fc6e233658b228b7ad9bacdaa23917afef
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648739"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Permitir o registo em ações de treino do Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O Azure Machine Learning Python SDK permite registar informações em tempo real utilizando o pacote de registo padrão python e a funcionalidade específica SDK. Pode registar-se localmente e enviar registos para o seu espaço de trabalho no portal.

Os registos podem ajudá-lo a diagnosticar erros e advertências, ou rastrear métricas de desempenho como parâmetros e desempenho do modelo. Neste artigo, aprende-se a permitir o registo de login nos seguintes cenários:

> [!div class="checklist"]
> * Sessões de formação interativas
> * Submeter trabalhos de formação usando ScriptRunConfig
> * `logging`Configurações nativas python
> * Registo de fontes adicionais


> [!TIP]
> Este artigo mostra-lhe como monitorizar o processo de treino de modelos. Se estiver interessado em monitorizar a utilização de recursos e eventos da Azure Machine learning, tais como quotas, ensaios de formação concluídos ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipos de dados

Pode registar vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens, diretórios e muito mais. Para obter mais informações e exemplos de código Python para diferentes tipos de dados, consulte a [página de referência da classe Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessão interativa de registo

As sessões de registo interativo são normalmente utilizadas em ambientes de cadernos. O método [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) inicia uma sessão de registo interativo. Quaisquer métricas registadas durante a sessão são adicionadas ao registo de execução na experiência. O método [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) termina as sessões e marca a execução como concluído.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig regista

Nesta secção, aprende-se a adicionar código de registo dentro das execuções scriptConfig. Pode utilizar a classe [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) para encapsular scripts e ambientes para execuções repetíveis. Também pode usar esta opção para mostrar um widget de Cadernos Jupyter visual para monitorização.

Este exemplo executa uma varredura de parâmetros sobre os valores alfa e captura os resultados usando o método [run.log().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----)

1. Crie um script de formação que inclua a lógica de registo, `train.py` .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Submeta o ```train.py``` script para ser executado num ambiente gerido pelo utilizador. Toda a pasta do guião é submetida para treino.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    O `show_output` parâmetro liga-se à registo verboso, que permite ver detalhes do processo de treino, bem como informações sobre quaisquer recursos remotos ou metas de computação. Utilize o seguinte código para ligar a registo verboso quando submeter a experiência.

```python
run = exp.submit(src, show_output=True)
```

Também pode utilizar o mesmo parâmetro na `wait_for_completion` função na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Registo nativo de Python

Alguns registos no SDK podem conter um erro que o instrui a definir o nível de registo para DEBUG. Para definir o nível de registo, adicione o seguinte código ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Fontes de registo adicionais

O Azure Machine Learning também pode registar informações de outras fontes durante o treino, como corridas automáticas de machine learning, ou contentores Docker que gerem os trabalhos. Estes registos não estão documentados, mas se encontrar problemas e contactar o suporte da Microsoft, poderão ser capazes de utilizar estes registos durante a resolução de problemas.

Para obter informações sobre métricas de registo no designer de aprendizagem automática Azure (pré-visualização), consulte [como registar métricas no designer (pré-visualização)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Blocos de notas de exemplo
O seguinte caderno demonstra conceitos neste artigo:
* [como usar-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Consulte estes artigos para saber mais sobre como usar a Azure Machine Learning:

* Saiba como [registar métricas no designer de aprendizagem automática Azure (pré-visualização)](how-to-track-designer-experiments.md).

* Veja um exemplo de como registar o melhor modelo e implantá-lo no tutorial, Treine um modelo de [classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).
