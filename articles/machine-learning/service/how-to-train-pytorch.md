---
title: Dar formação e registar modelos PyTorch
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como dar formação e registar um modelo de PyTorch usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: fc80fcde8de3fb2d6dd6f59804f6019b76aa8727
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295603"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar PyTorch modelos à escala com o serviço Azure Machine Learning

Este artigo mostra-lhe como dar formação e registar um modelo de PyTorch usando o serviço Azure Machine Learning. Baseia-se no [tutorial de aprendizagem de transferência do PyTorch](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) que cria um classificador de rede neurais profundas (DNN) para imagens de ants e abelhas.

[PyTorch](https://pytorch.org/) é uma estrutura de computacional de código-fonte aberto frequentemente utilizada para criar redes neurais profundas (DNN). Com o serviço do Azure Machine Learning, pode rapidamente aumentar horizontalmente trabalhos de treinamento de código-fonte aberto com recursos de computação na cloud elástica. Também pode controlar a execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo de PyTorch desde o início ou trazem um modelo existente para a cloud, serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um destes ambientes:

 - Azure Machine Learning bloco de notas do VM - nenhuma downloads ou de uma instalação necessária

    - Concluir o [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md) para criar um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo.
    - Na pasta de exemplos no servidor do bloco de notas, encontrar um bloco de notas concluído e expandido ao navegar para este diretório: **procedimentos-to-use-azureml > treinamento-com-aprendizagem profunda > train-hyperparameter-tune-deploy-with-pytorch** pasta. 
 
 - O seu servidor de bloco de notas do Jupyter

    - [Instalar o Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um ficheiro de configuração da área de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Transferir os ficheiros de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia sobre a página de exemplos do GitHub. O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente, a implementação de modelo e widgets de bloco de notas.

## <a name="set-up-the-experiment"></a>Configurar a experimentação

Esta secção define a experimentação de preparação por carregar os pacotes python necessários, a inicializar uma área de trabalho, criação de uma experimentação e carregar os dados de treinamento e os scripts de preparação.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicializar uma área de trabalho

O [área de trabalho do Azure Machine Learning serviço](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefactos de que criar. O SDK de Python, pode acessar os artefactos de área de trabalho através da criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de área de trabalho a partir da `config.json` ficheiro criado no [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "pytorch hymenoptera".

```Python
project_folder = './pytorch-hymenoptera'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-hymenoptera'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de dados consiste em cerca de 120 imagens de formação cada para ants e abelhas, com 75 imagens de validação para cada classe. Hymenoptera é a ordem de insetos, que inclui o ants e abelhas. Baixe e extraia o conjunto de dados como parte do nosso script de treinamento `pytorch_train.py`.

### <a name="prepare-training-scripts"></a>Preparar os scripts de preparação

Neste tutorial, o script de treinamento, `pytorch_train.py`, já é fornecido. Na prática, pode escolher qualquer script de treinamento personalizado, como está e executá-lo com o serviço Azure Machine Learning.

Carregar o script de treinamento de Pytorch `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se gostaria de utilizar o controlo de serviço do Azure Machine Learning e capacidades de métricas, terá de adicionar um pequena quantidade código dentro do script de treinamento. Exemplos de métricas de controle podem ser encontrados no `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a sua tarefa PyTorch ser executado no. Neste exemplo, crie um cluster de computação do Azure Machine Learning ativadas para GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre os destinos de computação, consulte a [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-pytorch-estimator"></a>Criar uma calculadora de PyTorch

O [estimador de PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma forma simples de iniciar uma tarefa de preparação de PyTorch num destino de computação.

O avaliador de PyTorch é implementado por meio de genérica [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser utilizado para suportar qualquer estrutura. Para obter mais informações sobre modelos com o avaliador de genérico de treinamento, consulte [formar modelos com o Azure Machine Learning utilizando estimator](how-to-train-ml-models.md)

Se o script de treinamento precisar pip adicional ou pacotes de conda para ser executado, pode ter os pacotes instalados na imagem de docker resultante ao transmitir os respetivos nomes através da `pip_packages` e `conda_packages` argumentos.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Submeter uma execução

O [executar o objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execuções, enquanto a tarefa está em execução e depois de concluída.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Como a execução é executada, executa as seguintes fases:

- **A preparar**: Uma imagem do docker é criada, de acordo com o avaliador de PyTorch. A imagem é carregada para container registry a área de trabalho e armazenada em cache para execuções posteriores. Os registos também são transmitidos para o histórico de execuções e podem ser visualizados para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar verticalmente, se o cluster do Batch AI requer mais nós para executar a execução que estão atualmente disponíveis.

- **Executar**: Todos os scripts na pasta de script são carregados para o destino de computação, arquivos de dados são montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs são transmitidas em fluxo para o histórico de execuções e pode ser utilizado para monitorizar a execução.

- **Pós-processamento**: A. / produz a pasta da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registar ou transferir um modelo

Depois de ter preparado o modelo, pode registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Também pode transferir uma cópia local do modelo, utilizando o objeto de execução. No script de treinamento `pytorch_train.py`, um PyTorch guardar o objeto persistir o modelo para uma pasta local (local para o destino de computação). Pode usar o objeto de execução para transferir uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Formação distribuída

O [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimator também suporta a formação distribuída em clusters GPU e CPU. Pode facilmente executar tarefas de PyTorch distribuídas e o serviço Azure Machine Learning irão gerir a orquestração para.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é um código fonte aberto, reduzir a estrutura para formação distribuída desenvolvidas por Uber. Ele oferece uma forma fácil de tarefas de GPU PyTorch distribuídas.

Para utilizar Horovod, especifique um [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) de objeto para o `distributed_training` parâmetro no construtor PyTorch. Esse parâmetro garante que a biblioteca Horovod está instalada para que possa utilizar no seu script de treinamento.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod e as respetivas dependências serão instaladas para, portanto, pode importá-lo no seu script de treinamento `train.py` da seguinte forma:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportar para ONNX

Para otimizar a inferência de tipos com o [ONNX Runtime](concept-onnx.md), converta seu modelo de PyTorch preparado em formato ONNX. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção. Consulte a [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) para obter um exemplo.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, treinados e registado um modelo de PyTorch no serviço Azure Machine Learning. Para saber como implementar um modelo, continue para o nosso artigo de implementação do modelo.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
