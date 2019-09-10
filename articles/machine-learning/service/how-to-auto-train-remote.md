---
title: Destinos de computação remota de ML automatizados
titleSuffix: Azure Machine Learning service
description: Saiba como criar modelos usando o Machine Learning automatizado em um Azure Machine Learning destino de computação remota com o serviço Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 5918cc3835d00536845a96ed81ef663867291e29
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858807"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Utilizar modelos com aprendizagem automática na cloud

No Azure Machine Learning, preparar o seu modelo em diferentes tipos de recursos de computação que gere. O destino de computação pode ser um computador local ou um recurso na nuvem.

Você pode facilmente escalar ou escalar horizontalmente seu experimento de aprendizado de máquina adicionando destinos de computação adicionais, como computação de Azure Machine Learning (AmlCompute). O AmlCompute é uma infraestrutura de computação gerenciada que permite que você crie facilmente uma computação de vários nós ou um único nó.

Neste artigo, você aprenderá a criar um modelo usando o ML automatizado com AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Remoto difere do local?

O tutorial "[treinar um modelo de classificação com o Machine Learning automatizado](tutorial-auto-train-models.md)" ensina a usar um computador local para treinar um modelo com ml automatizado. O fluxo de trabalho ao treinar localmente também se aplica a também destinos remotos. No entanto, com computação remota, automatizadas iterações de experimentação do ML são executadas assincronamente. Esta funcionalidade permite-lhe cancelar uma iteração específica, ver o estado da execução ou continuar a trabalhar em outras células no bloco de notas do Jupyter. Para treinar remotamente, você primeiro cria um destino de computação remota, como AmlCompute. Em seguida, configure o recurso remoto e submeter o seu código.

Este artigo mostra as etapas adicionais necessárias para executar um experimento de ML automatizado em um destino de AmlCompute remoto. O objeto de área de trabalho, `ws`, partir do tutorial é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar recurso

Crie o destino AmlCompute em seu espaço de`ws`trabalho () se ele ainda não existir.

**Tempo estimado**: A criação do destino AmlCompute leva aproximadamente 5 minutos.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Agora, pode utilizar o `compute_target` objeto como o destino de computação remota.

As restrições de nome de cluster incluem:
+ Tem de ser menor do que 64 carateres.
+ Não é possível incluir qualquer um dos seguintes carateres: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Acessar dados usando a função TabularDataset

Os X e y definidos `TabularDataset`como s, que são passados para o ml automatizado no AutoMLConfig. `from_delimited_files`Por padrão, o `infer_column_types` define como true, o que inferirá o tipo de colunas automaticamente. 

Se você deseja definir manualmente os tipos de coluna, pode definir o `set_column_types` argumento para definir manualmente o tipo de cada coluna. No exemplo de código a seguir, vêm os dados do pacote de sklearn.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Criar configuração de execução

Para tornar as dependências disponíveis para o script get_data. py, `RunConfiguration` defina um objeto `CondaDependencies`com definido. Use esse objeto para o `run_configuration` parâmetro no `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

Consulte este exemplo de [bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) para obter um exemplo adicional desse padrão de design.

## <a name="configure-experiment"></a>Configurar experimentação
Especifique as definições para `AutoMLConfig`.  (Consulte um [uma lista completa dos parâmetros](how-to-configure-auto-train.md#configure-experiment) e os respetivos valores possíveis.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Ativar explicações de modelo

Definir o opcional `model_explainability` parâmetro no `AutoMLConfig` construtor. Além disso, um objeto de pacote de dados de validação deve ser passado como um parâmetro `X_valid` para utilizar a funcionalidade de explainability do modelo.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>Submeter a experimentação de preparação

Submeta agora a configuração para selecionar automaticamente o algoritmo, os parâmetros de hyper e preparar o modelo.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Verá o resultado semelhante ao seguinte exemplo:

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


## <a name="explore-results"></a>Explorar os resultados

Você pode usar o mesmo [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , conforme mostrado no [tutorial de treinamento](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e uma tabela de resultados.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Eis uma imagem estática do widget.  No bloco de notas, pode clicar em qualquer linha da tabela para ver as propriedades de execução e os registos de saída para que são executados.   Também pode utilizar a lista pendente acima do gráfico para ver um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![desenho de widget](./media/how-to-auto-train-remote/plot.png)

O widget apresenta um URL que pode utilizar para ver e explorar o indivíduo detalhes da execução.  

Se você não estiver em um notebook Jupyter, poderá exibir a URL da própria execução:

```
remote_run.get_portal_url()
```

As mesmas informações estão disponíveis no seu espaço de trabalho.  Para saber mais sobre esses resultados, confira [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

### <a name="view-logs"></a>Ver registos

Localizar registos na DSVM em `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a>Melhor explicação do modelo

A obter dados de explicação do modelo permite-lhe ver informações detalhadas sobre os modelos para aumentar a transparência no que está sendo executado no back-end. Neste exemplo, execute explicações de modelo apenas para o melhor modelo de ajuste. Se executar para todos os modelos no pipeline, irá resultar num tempo de execução significativo. As informações de explicação do modelo incluem:

* shap_values: As informações de explicação geradas pela shap lib.
* expected_values: O valor esperado do modelo aplicado ao conjunto de dados X_train.
* overall_summary: Os valores de importância do recurso de nível de modelo são classificados em ordem decrescente.
* overall_imp: Os nomes de recurso classificados na mesma ordem que no overall_summary.
* per_class_summary: Os valores de importância do recurso de nível de classe classificados em ordem decrescente. Disponível somente para o caso de classificação.
* per_class_imp: Os nomes de recurso classificados na mesma ordem que no per_class_summary. Disponível somente para o caso de classificação.

Utilize o seguinte código para selecionar o pipeline melhor de sua iterações. O `get_output` método retorna a melhor execução e o modelo de ajustada para a invocação de acordo com a última.

```python
best_run, fitted_model = remote_run.get_output()
```

Importar o `retrieve_model_explanation` funcionar e executar o melhor modelo.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Imprimir os resultados para o `best_run` variáveis de explicação que pretende visualizar.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Imprimir a `best_run` resultados do resumo de variáveis de explicação na seguinte saída.

![Resultado da consola do modelo explainability](./media/how-to-auto-train-remote/expl-print.png)

Você também pode visualizar a importância do recurso por meio da interface do usuário do widget, da interface do usuário da Web no portal do Azure ou da [página inicial do espaço de trabalho (versão prévia)](https://ml.azure.com). 

![Modelo explainability da interface do Usuário](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exemplo

O notebook [How-to-use-azureml/Automated-Machine-Learning/Remote-amlcompute/auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) demonstra os conceitos neste artigo.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais [como configurar as definições para treinamento automática](how-to-configure-auto-train.md).
