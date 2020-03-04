---
title: Treine modelos de aprendizagem automática de ficção científica
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de formação de scikit-learn à escala empresarial utilizando a classe de estimadores Azure Machine Learning SKlearn. Os scripts de exemplo classificam as imagens de flores da íris para construir um modelo de aprendizagem automática baseado no conjunto de dados da íris do scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: d61e33568297e6f72aca0ab736f8a14f1758ffa1
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255135"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Construa modelos de scikit-learn à escala com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar os seus guiões de formação de scikit-learn à escala empresarial utilizando a classe de estimadores Azure Machine Learning [SKlearn.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

Os scripts exemplo neste artigo são usados para classificar imagens de flores de íris para construir um modelo de aprendizagem automática baseado no conjunto de dados da [íris](https://archive.ics.uci.edu/ml/datasets/iris)do scikit-learn .

Quer esteja a treinar um modelo de aprendizagem automática de ficção científica a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto utilizando recursos elásticos de computação em nuvem. Pode construir, implementar, versões e monitorizar modelos de produção com o Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:
 - Instância computacional Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de formação de amostras no servidor de cadernos, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** folder.

 - O seu próprio servidor jupyter Notebook

    - [Instale o SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Criar um ficheiro de [configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - Descarregue o conjunto de dados e o ficheiro de script de amostra 
        - [conjunto de dados da íris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia na página de amostras gitHub. O caderno inclui uma secção expandida que cobre a afinação inteligente do hiperparâmetro e a recuperação do melhor modelo por métricas primárias.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção configura a experiência de treino carregando os pacotes de python necessários, iniciando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e treinando scripts.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importar as bibliotecas piton necessárias.

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

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um objeto [`workspace`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)

Crie um objeto espaço de trabalho a partir do ficheiro `config.json` criado na [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Criar uma experiência de aprendizagem automática

Crie uma experiência e uma pasta para segurar os seus scripts de treino. Neste exemplo, crie uma experiência chamada "sklearn-íris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparar roteiro de treino

Neste tutorial, o roteiro de treino **train_iris.py** já está previsto para si. Na prática, você deve ser capaz de pegar qualquer script de treino personalizado como está e executá-lo com Azure ML sem ter que modificar o seu código.

Para utilizar as capacidades de rastreio e métricas Do Azure ML, adicione uma pequena quantidade de código Azure ML dentro do seu script de treino.  O roteiro de treino **train_iris.py** mostra como registar algumas métricas na sua execução Azure ML utilizando o `Run` objeto dentro do script.

O script de treino fornecido utiliza dados exemplo sada da função `iris = datasets.load_iris()`.  Para os seus próprios dados, poderá ter de utilizar passos como [o Upload dataset e scripts](how-to-train-keras.md#data-upload) para disponibilizar dados durante o treino.

Copie o guião de treino **train_iris.py** no seu diretório de projeto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um alvo de computação

Crie um alvo de computação para o seu trabalho de aprendizagem de ficção para correr. Scikit-learn apenas suporta um nó único, computação CPU.

O seguinte código cria uma computação gerida por Machine Learning (AmlCompute) para o seu recurso de computação de treino remoto. A criação de AmlCompute demora aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, este código irá ignorar o processo de criação.

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

Para obter mais informações sobre os alvos da computação, consulte o [que é um artigo-alvo de cálculo.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Criar um estimador de aprendizagem de ficção

O [estimador de aprendizagem de scikit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornece uma forma simples de lançar um trabalho de formação de ficção-aprendizagem num alvo de computação. É implementado através da classe [`SKLearn`,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) que pode ser usada para apoiar a formação de CPU de nó único.

Se o seu script de treino precisar de pacotes adicionais de pip ou conda para executar, pode ter os pacotes instalados na imagem de estivador resultante, passando os seus nomes através dos argumentos `pip_packages` e `conda_packages`.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após a sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: É criada uma imagem de estivador de acordo com o estimador TensorFlow. A imagem é enviada para o registo de contentores do espaço de trabalho e cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Escalação**: O cluster tenta aumentar a escala se o cluster de IA do lote necessitar de mais nós para executar a execução do que atualmente disponível.

- **Execução**: Todos os scripts da pasta script são enviados para o alvo da computação, as lojas de dados são montadas ou copiadas e a entry_script é executada. As saídas de dstout e a pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorizar o execução.

- **Pós-Processamento**: A pasta ./saídas do executo é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Guardar e registar o modelo

Depois de ter treinado o modelo, pode economizar e registá-lo no seu espaço de trabalho. O registo do modelo permite-lhe armazenar e verver os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação](concept-model-management-and-deployment.md)de modelos.

Adicione o seguinte código ao seu script de treino, train_iris.py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registe o modelo no seu espaço de trabalho com o seguinte código. Especificando os parâmetros `model_framework`, `model_framework_version`e `resource_configuration`, fica disponível a implementação do modelo sem código. Isto permite-lhe implementar diretamente o seu modelo como um serviço web a partir do modelo registado, e o `ResourceConfiguration` objeto define o recurso computacional para o serviço web.

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

O modelo que acabou de registar pode ser implementado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador utilizado para treinar. O modo de implementação contém uma secção sobre modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

### <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação do modelo sem código

Em vez da rota de implantação tradicional, também pode utilizar a funcionalidade de implementação sem código (pré-visualização)para o scikit-learn. A implementação de modelos sem código é suportada para todos os tipos de modelos de aprendizagem de scikit incorporados. Ao registar o seu modelo como mostrado acima com os parâmetros `model_framework`, `model_framework_version`e `resource_configuration`, pode simplesmente utilizar a função `deploy()` estática para implementar o seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: Estas dependências estão incluídas no recipiente de inferência de scikit-learn pré-construído.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

O completo [como cobrir](how-to-deploy-and-where.md) a implantação em Azure Machine Learning em maior profundidade.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou um modelo de aprendizagem de ficção científica, e aprendeu sobre opções de implementação. Veja estes outros artigos para saber mais sobre o Azure Machine Learning.

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Hiperparâmetros de melodia](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para formação distribuída de aprendizagem profunda em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
