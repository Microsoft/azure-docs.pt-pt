---
title: Treinar modelos de aprendizado de máquina com o scikit-Learn
titleSuffix: Azure Machine Learning service
description: Saiba como executar seus scripts de treinamento scikit-Learn em escala empresarial usando a classe SKlearn Estimator de Azure Machine Learning. Os scripts de exemplo classificam as imagens de flor íris para criar um modelo de aprendizado de máquina baseado no conjunto de informações de íris de scikit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5c44f4ed5cd6488520bf6dd99f502f33049b9b17
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963006"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Crie modelos scikit-Aprenda em escala com o serviço Azure Machine Learning

Neste artigo, saiba como executar seus scripts de treinamento scikit-Learn em escala empresarial usando a classe [estimada SKlearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) do Azure Machine Learning. 

Os scripts de exemplo neste artigo são usados para classificar imagens de flor de íris para criar um modelo de aprendizado de máquina baseado no [conjunto](https://archive.ics.uci.edu/ml/datasets/iris)de informações de íris do scikit.

Se você estiver treinando um modelo de aprendizado de máquina scikit-Learn do zero ou se estiver trazendo um modelo existente para a nuvem, poderá usar Azure Machine Learning para escalar trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, versão e monitorar modelos de nível de produção com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:
 - VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

    - Conclua [o tutorial: Ambiente de instalação e](tutorial-1st-experiment-sdk-setup.md) espaço de trabalho para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta de treinamento de exemplos no servidor de notebook, localize um bloco de anotações concluído e expandido navegando até este diretório: **instruções-uso-azureml > training > Train-hiperparameter-ajuste-deploy-com-sklearn** pasta.

 - Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - Baixar o conjunto de arquivos e o arquivo de script de exemplo 
        - [conjunto de uma íris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia na página de exemplos do github. O notebook inclui uma seção expandida que aborda o ajuste de hiperparâmetro inteligente e a recuperação do melhor modelo por métricas primárias.

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

O [espaço de trabalho de serviço do Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) trabalho criando um objeto.

Crie um objeto de espaço de `config.json` trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Criar um experimento do Machine Learning

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "sklearn-íris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Carregar conjunto de script e scripts

O [armazenamento](how-to-access-data.md) de dados é um local em que podem ser armazenados e acessados com a montagem ou a cópia dos dados no destino de computação. Cada espaço de trabalho fornece um repositório de armazenamento padrão. Carregue os dados e os scripts de treinamento para o datastore para que eles possam ser acessados facilmente durante o treinamento.

1. Crie o diretório para seus dados.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Carregue o conjunto de uma íris no repositório de armazenamento padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Carregue o script de treinamento scikit-Learn `train_iris.py`,.

    ```Python
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

O [estimador scikit-Learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento scikit-Learn em um destino de computação. Ela é implementada por [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) meio da classe, que pode ser usada para dar suporte ao treinamento de CPU de nó único.

Se o script de treinamento precisar de pacotes PIP ou Conda adicionais para execução, você poderá ter os pacotes instalados na imagem do Docker resultante passando seus nomes pelos `pip_packages` argumentos `conda_packages` e.

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

- **Preparando**: Uma imagem do Docker é criada de acordo com o estimador do TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Depois de treinar o modelo, você poderá salvá-lo e registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

Adicione o código a seguir ao seu script de treinamento, train_iris. py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registre o modelo em seu espaço de trabalho com o código a seguir.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Passos seguintes


Neste artigo, você treinou e registrou um modelo Keras no serviço Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de implantação de modelo.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
