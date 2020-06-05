---
title: Treine modelos de aprendizagem automática de scikit-learn
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de formação scikit-learn à escala de empresas utilizando a classe de estimador Azure Machine Learning SKlearn. Os scripts de exemplo classificam as imagens de flores da íris para construir um modelo de aprendizagem automática baseado no conjunto de dados da íris do scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 505211473820bdd3cdce1c2e28e27323d1053618
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433803"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Construa modelos de aprendizagem de scikit em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar os seus scripts de formação scikit-learn à escala de empresas utilizando a classe de estimador Azure Machine Learning [SKlearn.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

Os scripts de exemplo neste artigo são usados para classificar imagens de flores de íris para construir um modelo de aprendizagem automática baseado no conjunto de dados da [íris](https://archive.ics.uci.edu/ml/datasets/iris)do scikit-learn .

Quer esteja a treinar um modelo de aprendizagem automática de scikit-learn a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, versão e monitorizar modelos de nível de produção com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:
 - Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de formação de amostras no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > > de formação > pasta de sintonização de hiperparametro-com-sklearn.**

 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - Descarregue o conjunto de dados e o ficheiro de script de amostra 
        - [conjunto de dados íris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia na página de amostras do GitHub. O caderno inclui uma secção expandida que cobre a afinação inteligente de hiperparímetro e recuperação do melhor modelo por métricas primárias.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção define a experiência de treino carregando os pacotes de python necessários, inicializando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e scripts de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Criar uma experimentação de machine learning

Crie uma experiência e uma pasta para segurar os seus scripts de treino. Neste exemplo, crie uma experiência chamada "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

experiment = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparar roteiro de treino

Neste tutorial, o roteiro de treino **train_iris.py** já está previsto para si. Na prática, você deve ser capaz de pegar em qualquer script de treino personalizado como está e executá-lo com Azure ML sem ter que modificar o seu código.

Para utilizar as capacidades de rastreio e métricas Azure ML, adicione uma pequena quantidade de código Azure ML dentro do seu script de treino.  O script de treino **train_iris.py** mostra como registar algumas métricas na sua execução Azure ML usando o `Run` objeto dentro do script.

O script de formação fornecido utiliza dados de exemplo da `iris = datasets.load_iris()` função.  Para os seus próprios dados, poderá ter de utilizar etapas como [carregar conjunto de dados e scripts](how-to-train-keras.md#data-upload) para disponibilizar dados durante o treino.

Copie o script de treino **train_iris.py** no seu diretório de projeto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um alvo de computação

Crie um alvo de computação para o seu trabalho de aprendizagem de scikit. Scikit-learn suporta apenas nó, computação CPU.

O seguinte código, cria um computo gerido Azure Machine Learning (AmlCompute) para o seu recurso de computação de treino remoto. A criação da AmlCompute demora aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, este código saltará o processo de criação.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre metas de computação, consulte o [que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Criar um estimador de aprendizagem de scikit

O [estimador de aprendizagem de scikit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornece uma forma simples de lançar um trabalho de formação scikit-learn num alvo de computação. É implementado através da [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe, que pode ser usada para apoiar a formação de CPU de nó único.

Se o seu script de treino precisar de pacotes adicionais de pip ou conda para executar, pode ter os pacotes instalados na imagem do estivador resultante, passando os seus nomes através dos `pip_packages` argumentos e `conda_packages` argumentos.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py',
                    pip_packages=['joblib']
                   )
```


Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a corrida é executada, passa pelas seguintes fases:

- **Preparação:** Uma imagem de estivador é criada de acordo com o estimador TensorFlow. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas e o entry_script é executado. As saídas da sestada e da pasta ./logs são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Guardar e registar o modelo

Uma vez treinado o modelo, pode economize e registe no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

Adicione o seguinte código ao seu script de treino, train_iris.py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registe o modelo no seu espaço de trabalho com o seguinte código. Especificando os parâmetros `model_framework` `model_framework_version` , `resource_configuration` e, a implementação de nenhum modelo de código fica disponível. Isto permite-lhe implementar diretamente o seu modelo como um serviço web a partir do modelo registado, e o [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) objeto define o recurso compute para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementação

O modelo que acabou de registar pode ser implantado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador que usou para treinar. A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

### <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

Em vez da rota de implementação tradicional, também pode utilizar a funcionalidade de implementação sem código (pré-visualização)para o scikit-learn. A implementação de modelos sem código é suportada para todos os tipos de modelos de aprendizagem de scikit incorporados. Ao registar o seu modelo como mostrado acima com o `model_framework` `model_framework_version` , e `resource_configuration` parâmetros, pode simplesmente utilizar a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) função estática para implantar o seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: Estas dependências estão incluídas no recipiente de inferência scikit-learn pré-construído.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A forma completa [de como](how-to-deploy-and-where.md) fazer cobre a implantação em Azure Machine Learning em maior profundidade.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou um modelo de aprendizagem de scikit, e aprendeu sobre opções de implementação. Veja estes outros artigos para saber mais sobre Azure Machine Learning.

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
