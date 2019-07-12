---
title: Dar formação e registar scikit-saiba modelos
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como dar formação e registar um scikit-saiba modelar a utilizar o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840011"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar Scikit-saiba modelos à escala com o serviço Azure Machine Learning

Este artigo mostra-lhe como dar formação e registar um modelo Scikit-Saiba com o serviço Azure Machine Learning. Ele usa a popular [conjunto de dados íris](https://archive.ics.uci.edu/ml/datasets/iris) classificar as imagens de flor de íris com o personalizado [scikit-Saiba](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe.

Scikit-saiba é uma estrutura de computacional de código-fonte aberto frequentemente utilizada para o machine learning. Com o serviço do Azure Machine Learning, pode rapidamente aumentar horizontalmente trabalhos de treinamento de código-fonte aberto com recursos de computação na cloud elástica. Também pode controlar a execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo Scikit-saiba desde o início ou trazem um modelo existente para a cloud, serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um destes ambientes:
 - Azure Machine Learning bloco de notas do VM - nenhuma downloads ou de uma instalação necessária

    - Concluir o [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md) para criar um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo.
    - Na pasta de exemplos no servidor do bloco de notas, encontrar um bloco de notas concluído e expandido ao navegar para este diretório: **procedimentos-to-use-azureml > treinamento > train-hyperparameter-tune-deploy-with-sklearn** pasta.

 - O seu servidor de bloco de notas do Jupyter

    - [Instalar o Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um ficheiro de configuração da área de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Transfira o ficheiro de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia sobre a página de exemplos do GitHub. O bloco de notas inclui uma seção expandida abrangendo hiper-parâmetros inteligente ajuste e a obter o melhor modelo por métricas principais.

## <a name="set-up-the-experiment"></a>Configurar a experimentação

Esta secção define a experimentação de preparação por carregar os pacotes python necessários, a inicializar uma área de trabalho, criação de uma experimentação e carregar os dados de treinamento e os scripts de preparação.

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

### <a name="initialize-a-workspace"></a>Inicializar uma área de trabalho

O [área de trabalho do Azure Machine Learning serviço](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefactos de que criar. O SDK de Python, pode acessar os artefactos de área de trabalho através da criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de área de trabalho a partir da `config.json` ficheiro criado no [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "sklearn iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Carregar o conjunto de dados e scripts

O [arquivo de dados](how-to-access-data.md) é um local onde os dados podem ser armazenados e acedidos por montar ou copiar os dados para o destino de computação. Cada área de trabalho fornece um arquivo de dados padrão. Carregar os dados e os scripts de preparação para o arquivo de dados para que possam ser facilmente acedidos durante o treinamento.

1. Crie o diretório para os seus dados.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Carregar o conjunto de dados de iris para o arquivo de dados padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Script de preparação de carregamento a Scikit-saiba, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Crie um destino de computação para a sua tarefa Scikit-saiba ser executado no. Scikit saiba apenas suporta único nó, a computação de CPU.

O código seguinte, cria uma computação gerida do Azure Machine Learning (AmlCompute) para o seu recurso de computação de treinamento remoto. Criação de AmlCompute demora cerca de 5 minutos. Se o AmlCompute com esse nome já está em sua área de trabalho, esse código irá ignorar o processo de criação.

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

Para obter mais informações sobre os destinos de computação, consulte a [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-scikit-learn-estimator"></a>Criar uma calculadora Scikit-Saiba

O [Scikit-saiba estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de iniciar um Scikit-saiba a tarefa de preparação num destino de computação. Ele é implementado através da [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe, que pode ser utilizado para suportar o treinamento de CPU de nó único.

Se o script de treinamento precisar pip adicional ou pacotes de conda para ser executado, pode ter os pacotes instalados na imagem de docker resultante ao transmitir os respetivos nomes através da `pip_packages` e `conda_packages` argumentos.

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

## <a name="submit-a-run"></a>Submeter uma execução

O [executar o objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execuções, enquanto a tarefa está em execução e depois de concluída.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Como a execução é executada, executa as seguintes fases:

- **A preparar**: Uma imagem do docker é criada, de acordo com o avaliador de TensorFlow. A imagem é carregada para container registry a área de trabalho e armazenada em cache para execuções posteriores. Os registos também são transmitidos para o histórico de execuções e podem ser visualizados para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar verticalmente, se o cluster do Batch AI requer mais nós para executar a execução que estão atualmente disponíveis.

- **Executar**: Todos os scripts na pasta de script são carregados para o destino de computação, arquivos de dados são montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs são transmitidas em fluxo para o histórico de execuções e pode ser utilizado para monitorizar a execução.

- **Pós-processamento**: A. / produz a pasta da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Guardar e registe o modelo

Depois de ter preparado o modelo, pode guardar e registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

Adicione o seguinte código ao seu script de treinamento, train_iris.py, para guardar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registe o modelo para sua área de trabalho com o código a seguir.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, treinados e registado um modelo Scikit-saiba no serviço Azure Machine Learning.

* Para saber como implementar um modelo, continue para nosso [implementação de modelo](how-to-deploy-and-where.md) artigo.

* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)