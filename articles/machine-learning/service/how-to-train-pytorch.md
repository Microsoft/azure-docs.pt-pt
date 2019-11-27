---
title: Treinar modelos de PyTorch de aprendizado profundo
titleSuffix: Azure Machine Learning
description: Saiba como executar seus scripts de treinamento do PyTorch em escala empresarial usando a classe PyTorch estimador do Azure Machine Learning.  Os scripts de exemplo classificam as imagens de galinha e Turquia para criar uma rede neural de aprendizado profundo com base no tutorial de aprendizado de transferência do PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6eace81928664d97c2e3d1c80688add5f1ee3060
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213457"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Treine os modelos de aprendizado profundo do Pytorch em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como executar seus scripts de treinamento do [PyTorch](https://pytorch.org/) em escala empresarial usando a classe [estimada PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) do Azure Machine Learning.  

Os scripts de exemplo neste artigo são usados para classificar as imagens de galinha e Turquia para criar uma rede neural de aprendizado profundo com base no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizado de transferência do PyTorch. 

Se você está treinando um modelo de PyTorch de aprendizado profundo do zero ou está trazendo um modelo existente para a nuvem, você pode usar Azure Machine Learning para expandir trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, obter uma versão e monitorar modelos de nível de produção com Azure Machine Learning. 

Saiba mais sobre o [aprendizado profundo em relação ao aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

 - VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

    - Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta Samples Deep Learning no servidor do notebook, encontre um bloco de anotações completo e expandido navegando até este diretório: **instruções-uso-azureml > treinamento com aprendizado > Train-hiperparameter-ajuste-deploy-com-pytorch** pasta. 
 
 - Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixe os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

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

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um objeto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Criar um experimento de aprendizado profundo

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "pytorch-pássaros".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de informações consiste em cerca de 120 imagens de treinamento para turkeys e Chickens, com imagens de validação de 100 para cada classe. Vamos baixar e extrair o conjunto de um como parte do nosso script de treinamento `pytorch_train.py`. As imagens são um subconjunto do [conjunto de imagens do Open images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparar scripts de treinamento

Neste tutorial, o script de treinamento, `pytorch_train.py`, já foi fornecido. Na prática, você pode usar qualquer script de treinamento personalizado, como está, e executá-lo com Azure Machine Learning.

Carregue o script de treinamento do Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se você quiser usar recursos de rastreamento e métricas de Azure Machine Learning, precisará adicionar um pequeno código de valor dentro do script de treinamento. Exemplos de rastreamento de métricas podem ser encontrados em `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho do PyTorch. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

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

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

## <a name="create-a-pytorch-estimator"></a>Criar um estimador PyTorch

O [estimador do PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento do PyTorch em um destino de computação.

O estimador PyTorch é implementado por meio da classe [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) genérica, que pode ser usada para dar suporte a qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [treinar modelos com Azure Machine Learning usando o estimador](how-to-train-ml-models.md)

Se o script de treinamento precisar de pacotes PIP ou Conda adicionais para execução, você poderá ter os pacotes instalados na imagem do Docker resultante passando seus nomes por meio dos argumentos `pip_packages` e `conda_packages`.

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

## <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o estimador do PyTorch. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registrar ou baixar um modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> O modelo que você acabou de registrar é implantado exatamente da mesma maneira que qualquer outro modelo registrado no Azure Machine Learning, independentemente do estimador usado para treinamento. A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script de treinamento `pytorch_train.py`, um objeto PyTorch Save persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Preparação distribuída

O estimador de [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) também dá suporte ao treinamento distribuído em clusters de CPU e GPU. Você pode executar facilmente trabalhos PyTorch distribuídos e Azure Machine Learning gerenciará a orquestração para você.

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre, tudo que reduz o treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para trabalhos de PyTorch de GPU distribuídos.

Para usar Horovod, especifique um objeto [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para o parâmetro `distributed_training` no Construtor PyTorch. Esse parâmetro garante que a biblioteca Horovod esteja instalada para que você use em seu script de treinamento.


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
Horovod e suas dependências serão instaladas para você, para que você possa importá-las em seu script de treinamento `train.py` da seguinte maneira:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportar para ONNX

Para otimizar a inferência com o [tempo de execução ONNX](concept-onnx.md), converta seu modelo PyTorch treinado para o formato ONNX. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. Consulte o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) para obter um exemplo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registrou uma rede neural de aprendizado profundo usando PyTorch em Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de implantação de modelo.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Rastrear métricas de execução durante o treinamento](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
