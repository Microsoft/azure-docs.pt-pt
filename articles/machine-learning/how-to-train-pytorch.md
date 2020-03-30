---
title: Treine modelos pyTorch de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de treino PyTorch à escala empresarial utilizando a classe de estimativa pyTorch da Azure Machine Learning.  Os roteiros exemplo suclassificam imagens de frango e peru para construir uma rede neural de aprendizagem profunda baseada no tutorial de aprendizagem de transferências da PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834865"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Treine modelos de aprendizagem profunda do Pircha em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar os seus scripts de treino [PyTorch](https://pytorch.org/) à escala empresarial utilizando a classe de [estimativa pyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) da Azure Machine Learning.  

Os roteiros exemplo neste artigo são usados para classificar imagens de frango e peru para construir uma rede neural de aprendizagem profunda baseada no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizagem de transferências da PyTorch. 

Quer esteja a treinar um modelo PyTorch de aprendizagem profunda a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, versão e monitorizar modelos de produção com o Azure Machine Learning. 

Saiba mais sobre [aprendizagem profunda vs machine learning.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- Instância computacional Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras, na pasta de aprendizagem profunda do servidor do caderno, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > treino-com-aprendizagem profunda > pasta de metímetro-mete-se-com-pytorch.** 
 
 - O seu próprio servidor jupyter Notebook

    - [Instale o SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Criar um ficheiro de [configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script da amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de amostras gitHub. O caderno inclui secções expandidas que cobrem a finação inteligente do hiperparâmetro, a implementação do modelo e widgets de caderno.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção configura a experiência de treino carregando os pacotes de python necessários, iniciando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e treinando scripts.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importar as bibliotecas piton necessárias.

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

Crie um objeto `config.json` espaço de trabalho a partir do ficheiro criado na [secção pré-requisitos](#prerequisites).

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

O conjunto de dados é composto por cerca de 120 imagens de treino cada para perus e galinhas, com 100 imagens de validação para cada classe. Vamos descarregar e extrair o conjunto de `pytorch_train.py`dados como parte do nosso script de treino. As imagens são um subconjunto do Conjunto de [Dados Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparar scripts de treino

Neste tutorial, o roteiro `pytorch_train.py`de treino, já está fornecido. Na prática, você pode pegar qualquer script de treino personalizado, como é, e executá-lo com Azure Machine Learning.

Faça upload do guião de treino do Pytorch. `pytorch_train.py`

```Python
shutil.copy('pytorch_train.py', project_folder)
```

No entanto, se quiser utilizar capacidades de rastreio e métricas de Machine Learning Azure, terá de adicionar um pequeno código de quantidade dentro do seu script de treino. Exemplos de rastreio de métricas podem ser encontrados em `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de cálculo para o seu trabalho pyTorch para continuar. Neste exemplo, crie um cluster de computação azure machine learning ativado por GPU.

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

Para obter mais informações sobre os alvos da computação, consulte o [que é um artigo-alvo de cálculo.](concept-compute-target.md)

## <a name="create-a-pytorch-estimator"></a>Criar um estimador PyTorch

O [estimador PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornece uma forma simples de lançar um trabalho de treino pyTorch num alvo de computação.

O estimador PyTorch é implementado [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para suportar qualquer enquadramento. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte modelos de [comboio com Machine Learning Azure utilizando estimador](how-to-train-ml-models.md)

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

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após a sua conclusão.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: Uma imagem de estivador é criada de acordo com o estimador PyTorch. A imagem é enviada para o registo de contentores do espaço de trabalho e cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Escalação**: O cluster tenta aumentar a escala se o cluster de IA do lote necessitar de mais nós para executar a execução do que atualmente disponível.

- **Execução**: Todos os scripts da pasta script são enviados para o alvo da computação, as lojas de dados são montadas ou copiadas e a entry_script é executada. As saídas de dstout e a pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorizar o execução.

- **Pós-Processamento**: A pasta ./saídas do executo é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registe ou descarregue um modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo do modelo permite-lhe armazenar e verver os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação](concept-model-management-and-deployment.md)de modelos.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> O modelo que acabou de registar é implementado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador utilizado para treinar. O modo de implementação contém uma secção sobre modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode descarregar uma cópia local do modelo utilizando o objeto Run. No script `pytorch_train.py`de treino, um objeto de salvamento PyTorch persiste o modelo para uma pasta local (local para o alvo da computação). Pode utilizar o objeto Run para descarregar uma cópia.

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

O [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimador também apoia a formação distribuída através de clusters CPU e GPU. Você pode facilmente executar trabalhos pyTorch distribuídos e Azure Machine Learning vai gerir a orquestração para você.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é uma fonte aberta, tudo reduz o quadro para a formação distribuída desenvolvida pela Uber. Oferece um caminho fácil para trabalhos de GPU PyTorch distribuídos.

Para utilizar o Horovod, especifique um [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objeto para o `distributed_training` parâmetro no construtor PyTorch. Este parâmetro garante que a biblioteca Horovod está instalada para que possa utilizar no seu script de treino.


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
Horovod e as suas dependências serão instaladas para si, `train.py` para que possa importá-la no seu roteiro de treino da seguinte forma:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportação para ONNX

Para otimizar a inferência com o Tempo de [Execução ONNX,](concept-onnx.md)converta o seu modelo PyTorch treinado para o formato ONNX. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção. Consulte o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) por exemplo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou uma rede neural de aprendizagem profunda utilizando o PyTorch no Azure Machine Learning. Para aprender a implementar um modelo, continue para o nosso artigo de implementação de modelos.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para formação distribuída de aprendizagem profunda em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

