---
title: Treinar rede neural de aprendizado profundo com o chainer
titleSuffix: Azure Machine Learning
description: Saiba como executar seus scripts de treinamento do PyTorch em escala empresarial usando a classe de estimador de Azure Machine Learning de encadeamento.  O script de exemplo classifica imagens de dígitos manuscritos para criar uma rede neural de aprendizado profundo usando a biblioteca do Python do sequenciador em execução na parte superior do numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: 8427bd8532895e95de82a6ed577052971f15a25d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584515"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Treinar e registrar modelos de encadeamento em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como executar os scripts de treinamento do [encadeamento](https://chainer.org/) em escala empresarial usando a classe de [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) de Azure Machine Learning de encadeamento. O script de treinamento de exemplo neste artigo usa o popular [conjunto de MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos usando uma DNN (rede neural profunda) criada usando a Biblioteca Python do Chainer em execução na parte superior do [numpy](https://www.numpy.org/).

Se você está treinando um modelo de encadeamento de aprendizado profundo do zero ou está trazendo um modelo existente para a nuvem, você pode usar Azure Machine Learning para escalar trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, obter uma versão e monitorar modelos de nível de produção com Azure Machine Learning. 

Saiba mais sobre o [aprendizado profundo em relação ao aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

    - Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta de aprendizado profundo de exemplos no servidor do notebook, encontre um bloco de anotações e arquivos concluídos no **> de instruções-uso-azureml-estruturas > chain > a implantação > Train-hiperparameter-ajustar-implantar-com-a pasta do Chainr** .  O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

- Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - Baixe o arquivo de script de exemplo [chainer_mnist. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe a biblioteca do azureml. Core Python e exiba o número de versão.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um objeto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Crie um objeto de espaço de trabalho lendo o arquivo de `config.json` criado na [seção pré-requisitos](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Criar um diretório do projeto
Crie um diretório que conterá todo o código necessário do computador local ao qual você precisará de acesso no recurso remoto. Isso inclui o script de treinamento e quaisquer arquivos adicionais dos quais o script de treinamento depende.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparar script de treinamento

Neste tutorial, o script de treinamento **chainer_mnist. py** já foi fornecido para você. Na prática, você deve ser capaz de pegar qualquer script de treinamento personalizado como está e executá-lo com o Azure ML sem precisar modificar seu código.

Para usar os recursos de acompanhamento e métricas do Azure ML, adicione uma pequena quantidade de código do Azure ML dentro de seu script de treinamento.  O script de treinamento **chainer_mnist. py** mostra como registrar algumas métricas em sua execução do Azure ml usando o objeto `Run` dentro do script.

O script de treinamento fornecido usa dados de exemplo da função de `datasets.mnist.get_mnist` de encadeamento.  Para seus próprios dados, talvez seja necessário usar etapas como [carregar DataSet e scripts](how-to-train-keras.md#data-upload) para disponibilizar os dados durante o treinamento.

Copie o script de treinamento **chainer_mnist. py** para o diretório do projeto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Criar um experimento de aprendizado profundo

Crie um experimento. Neste exemplo, crie um experimento chamado "Chainr-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Você precisa de um [destino de computação](concept-compute-target.md) para treinar seu modelo. Neste exemplo, você usa a computação gerenciada do Azure ML (AmlCompute) para o recurso de computação de treinamento remoto.

A **criação de AmlCompute leva aproximadamente 5 minutos**. Se o AmlCompute com esse nome já estiver em seu espaço de trabalho, esse código ignorará o processo de criação.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Criar um estimador de encadeamento

O [avaliador do encadeamento](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornece uma maneira simples de iniciar trabalhos de treinamento de encadeamento em seu destino de computação.

O avaliador do encadeamento é implementado por meio da classe de [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) genérica, que pode ser usada para dar suporte a qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [treinar modelos com Azure Machine Learning usando o estimador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o avaliador do sequenciador. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Depois de treinar o modelo, você poderá salvá-lo e registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).


Após a conclusão do treinamento do modelo, registre o modelo em seu espaço de trabalho com o código a seguir.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Se você receber um erro de que o modelo não foi encontrado, dê um minuto e tente novamente.  Às vezes, há um ligeiro atraso entre o fim da execução do treinamento e a disponibilidade do modelo no diretório de saídas.

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer o trabalho de validação de modelo adicional localmente. No script de treinamento, `chainer_mnist.py`, um objeto de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto executar para baixar uma cópia do repositório de armazenamento.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registrou uma rede neural de aprendizado profundo usando o chainer em Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de [implantação de modelo](how-to-deploy-and-where.md) .

* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Rastrear métricas de execução durante o treinamento](how-to-track-experiments.md)

* [Veja nossa arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
