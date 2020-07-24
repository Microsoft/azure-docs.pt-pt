---
title: Treine modelos PyTorch de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de treinamento PyTorch à escala da empresa utilizando a classe de estimador PyTorch da Azure Machine Learning.  Os scripts de exemplo classificam as imagens de frango e peru para construir uma rede neural de aprendizagem profunda baseada no tutorial de aprendizagem de transferência de PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8c22ba456e8d4beaa2295485567ff08a27abc457
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060726"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Treinar pytorch modelos de aprendizagem profunda em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar os seus scripts de treinamento [PyTorch](https://pytorch.org/) à escala empresarial utilizando a classe [de estimador PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) da Azure Machine Learning.  

Os scripts de exemplo neste artigo são usados para classificar imagens de frango e peru para construir uma rede neural de aprendizagem profunda baseada no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizagem de transferência de PyTorch. 

Quer esteja a treinar um modelo PyTorch de aprendizagem profunda a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, verrativa e monitorizar modelos de nível de produção com Azure Machine Learning. 

Saiba mais sobre [aprendizagem profunda vs machine learning.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:

- Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras da pasta de aprendizagem profunda no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > treino com aprendizagem profunda > pasta train-hyperparameter-tune-deploy-with-pytorch.** 
 
 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de amostras do GitHub. O caderno inclui secções expandidas que cobrem afinação inteligente de hiperparímetros, implantação de modelos e widgets de portátil.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção define a experiência de treino carregando os pacotes de python necessários, inicializando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e scripts de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

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

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Criar uma experiência de aprendizagem profunda

Crie uma experiência e uma pasta para segurar os seus scripts de treino. Neste exemplo, crie uma experiência chamada "pytorch-birds".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de dados consiste em cerca de 120 imagens de treino cada para perus e galinhas, com 100 imagens de validação para cada turma. Vamos descarregar e extrair o conjunto de dados como parte do nosso script de `pytorch_train.py` treino. As imagens são um subconjunto do Conjunto de [Dados V5 Imagens Abertas](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparar scripts de treino

Neste tutorial, o roteiro de `pytorch_train.py` formação, já está fornecido. Na prática, você pode pegar qualquer script de treino personalizado, como está, e executá-lo com Azure Machine Learning.

Faça o upload do roteiro de treino do Pytorch, `pytorch_train.py` .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se quiser utilizar as capacidades de rastreio e métricas de aprendizagem automática do Azure, terá de adicionar um pequeno código de quantidade dentro do seu script de treino. Exemplos de rastreio de métricas podem ser encontrados em `pytorch_train.py` .

## <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de computação para o seu trabalho PyTorch funcionar. Neste exemplo, crie um cluster de cálculo de aprendizagem automática Azure ativado pela GPU.

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Para obter mais informações sobre metas de computação, consulte o [que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-pytorch-estimator"></a>Criar um estimador PyTorch

O [estimador PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma forma simples de lançar um trabalho de treino PyTorch num alvo de computação.

O estimador PyTorch é implementado através da [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe genérica, que pode ser usada para suportar qualquer enquadramento. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte [os modelos de comboio com Azure Machine Learning utilizando o estimador](how-to-train-ml-models.md)

Se o seu script de treino precisar de pacotes adicionais de pip ou conda para executar, pode ter os pacotes instalados na imagem do estivador resultante, passando os seus nomes através dos `pip_packages` argumentos e `conda_packages` argumentos.

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

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados através de uma [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: Uma imagem de estivador é criada de acordo com o estimador PyTorch. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas e o entry_script é executado. As saídas da sestada e da pasta ./logs são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registe-se ou descarregue um modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> O modelo que acabou de registar é implantado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador que usou para treinar. A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode descarregar uma cópia local do modelo utilizando o objeto Executar. No roteiro de `pytorch_train.py` treino, um objeto de salvamento PyTorch persiste o modelo para uma pasta local (local para o alvo do cálculo). Pode utilizar o objeto Executar para descarregar uma cópia.

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

O [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimador também apoia a formação distribuída em clusters de CPU e GPU. Você pode facilmente executar trabalhos PyTorch distribuídos e Azure Machine Learning vai gerir a orquestração para você.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) é uma fonte aberta, todas reduzem o enquadramento para a formação distribuída desenvolvida pela Uber. Oferece um caminho fácil para trabalhos distribuídos da GPU PyTorch.

Para utilizar o Horovod, especifique um [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objeto para o parâmetro no construtor `distributed_training` PyTorch. Este parâmetro garante que a biblioteca Horovod está instalada para que possa utilizar no seu script de treino.


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
Horovod e as suas dependências serão instaladas para si, para que possa importá-lo no seu roteiro de treino `train.py` da seguinte forma:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportação para ONNX

Para otimizar a inferência com o [Tempo de Execução ONNX,](concept-onnx.md)converta o seu modelo PyTorch treinado para o formato ONNX. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção. Veja o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) como exemplo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registou uma rede neural de aprendizagem profunda usando PyTorch em Azure Machine Learning. Para aprender a implementar um modelo, continue para o nosso artigo de implementação de modelos.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

