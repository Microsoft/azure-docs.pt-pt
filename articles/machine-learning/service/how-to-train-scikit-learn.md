---
title: Treine o scikit-Aprenda os modelos de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como executar seus scripts de treinamento scikit-Learn em escala empresarial usando a classe SKlearn Estimator Azure Machine Learning. Os scripts de exemplo classificam as imagens de flor íris para criar um modelo de aprendizado de máquina baseado no conjunto de informações de íris de scikit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 2b6cdf9350d95de901e8a0f1e875d90513b33f1a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976116"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Crie modelos scikit-Aprenda em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como executar seus scripts de treinamento scikit-Learn em escala empresarial usando a classe Azure Machine Learning [SKlearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) . 

Os scripts de exemplo neste artigo são usados para classificar imagens de flor de íris para criar um modelo de aprendizado de máquina baseado no [conjunto](https://archive.ics.uci.edu/ml/datasets/iris)de informações de íris do scikit.

Se você estiver treinando um modelo de aprendizado de máquina scikit-Learn do zero ou se estiver trazendo um modelo existente para a nuvem, poderá usar Azure Machine Learning para escalar trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, versão e monitorar modelos de nível de produção com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:
 - VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

    - Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta de treinamento de exemplos no servidor do notebook, localize um bloco de anotações concluído e expandido navegando até este diretório: **instruções-uso-azureml > ml-frameworks > scikit-saiba > treinamento > Train-hiperparameter-ajuste-deploy-com-sklearn** pasta.

 - Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - Baixar o conjunto de arquivos e o arquivo de script de exemplo 
        - [conjunto de uma íris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia na página de exemplos do github. O notebook inclui uma seção expandida que aborda o ajuste de hiperparâmetro inteligente e a recuperação do melhor modelo por métricas primárias.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

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

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um objeto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Criar uma experimentação de machine learning

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "sklearn-íris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparar script de treinamento

Neste tutorial, o script de treinamento **train_iris. py** já foi fornecido para você. Na prática, você deve ser capaz de pegar qualquer script de treinamento personalizado como está e executá-lo com o Azure ML sem precisar modificar seu código.

Para usar os recursos de métrica e acompanhamento do ML do Azure, adicione uma pequena quantidade de código do Azure ML dentro de seu script de treinamento.  O script de treinamento **train_iris. py** mostra como registrar algumas métricas em sua execução do Azure ml usando o objeto `Run` dentro do script.

O script de treinamento fornecido usa dados de exemplo da função `iris = datasets.load_iris()`.  Para seus próprios dados, talvez seja necessário usar etapas como [carregar DataSet e scripts](how-to-train-keras.md#data-upload) para disponibilizar os dados durante o treinamento.

Copie o script de treinamento **train_iris. py** para o diretório do projeto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Crie um destino de computação para o trabalho scikit-learn a ser executado. Scikit-Learn dá suporte apenas a computação de CPU e de nó único.

O código a seguir cria uma AmlCompute (computação gerenciada Azure Machine Learning) para o recurso de computação de treinamento remoto. A criação de AmlCompute leva aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver em seu espaço de trabalho, esse código irá ignorar o processo de criação.

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

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Criar um estimador scikit-Learn

O [estimador scikit-Learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento scikit-Learn em um destino de computação. Ele é implementado por meio da classe [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , que pode ser usada para dar suporte ao treinamento de CPU de nó único.

Se o script de treinamento precisar de pacotes PIP ou Conda adicionais para execução, você poderá ter os pacotes instalados na imagem do Docker resultante passando seus nomes por meio dos argumentos `pip_packages` e `conda_packages`.

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

## <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o estimador do TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Depois de treinar o modelo, você poderá salvá-lo e registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

Adicione o código a seguir ao seu script de treinamento, train_iris. py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registre o modelo em seu espaço de trabalho com o código a seguir. Ao especificar os parâmetros `model_framework`, `model_framework_version`e `resource_configuration`, a implantação do modelo sem código fica disponível. Isso permite que você implante diretamente seu modelo como um serviço Web do modelo registrado, e o objeto `ResourceConfiguration` define o recurso de computação para o serviço Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementação

O modelo que você acabou de registrar pode ser implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente do estimador usado para treinamento. A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

### <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (versão prévia) para scikit-learn. A implantação de modelo sem código tem suporte para todos os tipos de modelo scikit-Learn internos. Ao registrar seu modelo, conforme mostrado acima com os parâmetros `model_framework`, `model_framework_version`e `resource_configuration`, você pode simplesmente usar a função estática `deploy()` para implantar seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Observação: essas dependências são incluídas no contêiner de inferência scikit-Learn predefinido.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A implantação [completa abrange](how-to-deploy-and-where.md) em Azure Machine Learning mais detalhadamente.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registrou um modelo scikit-Learn e aprendeu sobre as opções de implantação. Consulte estes outros artigos para saber mais sobre Azure Machine Learning.

* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
