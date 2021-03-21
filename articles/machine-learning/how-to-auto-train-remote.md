---
title: Metas de computação remota automatizada ml
titleSuffix: Azure Machine Learning
description: Aprenda a construir modelos usando machine learning automatizado em um alvo de computação remota Azure Machine Learning com Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 03/09/2020
ms.openlocfilehash: 402af581e0ca6de9936b78106840a4fe273069d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503458"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Preparar modelos com machine learning automatizado na cloud



No Azure Machine Learning, treina o seu modelo em diferentes tipos de recursos computacional que gere. O alvo do cálculo pode ser um computador local ou um recurso na nuvem.

Pode escalar facilmente ou escalar a sua experiência de machine learning adicionando alvos computacional adicionais, como Azure Machine Learning Compute (AmlCompute). A AmlCompute é uma infraestrutura de computação gerida que lhe permite criar facilmente uma computação de nó único ou de vários nós.

Neste artigo, aprende-se a construir um modelo utilizando ML automatizado com AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Como é que o controlo remoto difere do local?

Mais funcionalidades estão disponíveis quando utiliza um alvo de computação remota.  Para mais detalhes, consulte [os alvos de computação local e remota.](concept-automated-ml.md#local-remote)

O tutorial "[Treine um modelo de classificação com machine learning automatizado](tutorial-auto-train-models.md)" ensina-o a usar um computador local para treinar um modelo com ML automatizado. O fluxo de trabalho quando se treina localmente também se aplica a alvos remotos. Para treinar remotamente, primeiro cria um alvo de computação remota, como a AmlCompute. Em seguida, configura o recurso remoto e envia o seu código lá.

Este artigo mostra os passos adicionais necessários para executar uma experiência automatizada de ML num alvo remoto da AmlCompute. O objeto do espaço `ws` de trabalho, do tutorial é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar um recurso

Crie o [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29) alvo no seu espaço de trabalho ( ) se já não `ws` existir.

**Estimativa de tempo**: A criação do alvo AmlCompute demora aproximadamente 5 minutos.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Agora pode usar o `compute_target` objeto como alvo de computação remota.

As restrições de nome do cluster incluem:
+ Deve ter menos de 64 caracteres.
+ Não é possível incluir nenhum dos seguintes caracteres: `\` ~ ! @ $ % ^ ^ & * ( ) = = [ ] { } } \\ \\ | ; " "  < > \' \\ / ?. `

## <a name="access-data-using-tabulardataset-function"></a>Aceder a dados utilizando a função TabularDataset

Definido training_data como [`TabularDataset`](/python/api/azureml-core/azureml.data.tabulardataset) e o rótulo, que são passados para ML automatizado no [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . O `TabularDataset` `from_delimited_files` método, por padrão, define o `infer_column_types` verdadeiro, que infere automaticamente o tipo de colunas. 

Se desejar definir manualmente os tipos de colunas, pode definir o `set_column_types` argumento para definir manualmente o tipo de cada coluna. Na amostra de código a seguir, os dados provêm do pacote sklearn.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Experiência de configuração
Especificar as definições para `AutoMLConfig` .  (Ver uma [lista completa dos parâmetros](how-to-configure-auto-train.md#configure-experiment) e dos seus valores possíveis.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Submeter experiência de formação

Agora envie a configuração para selecionar automaticamente o algoritmo, hiper-parâmetros e treinar o modelo.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'Tutorial-automl-remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Verá uma saída semelhante ao seguinte exemplo:

```output
Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
***********************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE:  A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
***********************************************************************************************

 ITERATION     PIPELINE                               DURATION                METRIC      BEST
         2      Standardize SGD classifier            0:02:36                  0.954     0.954
         7      Normalizer DT                         0:02:22                  0.161     0.954
         0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
         4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
         1      Normalizer kNN                        0:02:44                  0.984     0.984
         9      Normalizer extra trees                0:03:15                  0.834     0.984
         5      Robust Scaler DT                      0:02:18                  0.736     0.984
         8      Standardize kNN                       0:02:05                  0.981     0.984
         6      Standardize SVM                       0:02:18                  0.984     0.984
        10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
        11      Standardize SGD classifier            0:02:24                  0.863     0.984
         3      Standardize gradient boosting         0:03:03                  0.971     0.984
        12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
        14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
        13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
        15      Robust Scaler kNN                     0:02:28                  0.904     0.989
        17      Standardize kNN                       0:02:22                  0.974     0.989
        16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
        18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
        19      Robust Scaler kNN                     0:02:32                  0.983     0.989
```

## <a name="explore-results"></a>Explorar resultados

Você pode usar o mesmo [widget Jupyter](/python/api/azureml-widgets/azureml.widgets) como mostrado no [tutorial de treino](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e tabela de resultados.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Eis uma imagem estática do widget.  No caderno, pode clicar em qualquer linha da tabela para ver propriedades de execução e registos de saída para essa execução.   Também pode utilizar o dropdown acima do gráfico para visualizar um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![desenho de widget](./media/how-to-auto-train-remote/plot.png)

O widget apresenta um URL que pode usar para ver e explorar os detalhes individuais da execução.  

Se não estiver num bloco de notas jupyter, pode exibir o URL da própria execução:

```
remote_run.get_portal_url()
```

A mesma informação está disponível no seu espaço de trabalho.  Para saber mais sobre estes resultados, consulte [os resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)

## <a name="example"></a>Exemplo

O [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) que se segue demonstra conceitos neste artigo.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Saiba [como configurar as definições para o treino automático](how-to-configure-auto-train.md).
* Consulte o [modo de](how-to-machine-learning-interpretability-automl.md) ativar as funcionalidades de interpretação do modelo em experiências automatizadas de ML.