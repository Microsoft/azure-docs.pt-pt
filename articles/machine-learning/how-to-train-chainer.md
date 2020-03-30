---
title: Treine modelos de cadeia de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de treino PyTorch à escala empresarial utilizando a classe de estimadores de cadeia de aprendizagem automática Azure Machine Learning.  O roteiro de exemplo classifica imagens de dígitos manuscritas para construir uma rede neural de aprendizagem profunda usando a biblioteca Chainer Python correndo em cima de dormente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536619"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Treine e registe modelos Chainer em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar os seus scripts de formação [Chainer](https://chainer.org/) à escala empresarial utilizando a classe de estimadores azure machine learning [chainer.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) O roteiro de formação de exemplo neste artigo usa o popular conjunto de [dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar os dígitos manuscritos usando uma rede neural profunda (DNN) construída usando a biblioteca Chainer Python funcionando em cima de [dorpy](https://www.numpy.org/).

Quer esteja a treinar um modelo de chainer de aprendizagem profunda a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, versão e monitorizar modelos de produção com o Azure Machine Learning. 

Saiba mais sobre [aprendizagem profunda vs machine learning.](concept-deep-learning-vs-machine-learning.md)

Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- Instância computacional Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras, na pasta de aprendizagem profunda do servidor do portátil, encontre um caderno e ficheiros completos na > de forma a **utilizar-em-azul-quadros > cadeia > implantação >** pasta de implantação de hiperparâmetros-com corrente.  O caderno inclui secções expandidas que cobrem a finação inteligente do hiperparâmetro, a implementação do modelo e widgets de caderno.

- O seu próprio servidor jupyter Notebook

    - [Instale o SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Criar um ficheiro de [configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - Descarregue o ficheiro de script da amostra [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) deste guia na página de amostras gitHub. O caderno inclui secções expandidas que cobrem a finação inteligente do hiperparâmetro, a implementação do modelo e widgets de caderno.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção configura a experiência de treino carregando os pacotes de python necessários, iniciando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e treinando scripts.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe a biblioteca Python azureml.core e exiba o número da versão.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Crie um objeto espaço `config.json` de trabalho lendo o ficheiro criado na [secção pré-requisitos:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Criar um diretório de projeto
Crie um diretório que contenha todo o código necessário da sua máquina local a que necessitará de acesso no recurso remoto. Isto inclui o script de treino e quaisquer ficheiros adicionais de que o seu script de treino dependa.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparar roteiro de treino

Neste tutorial, o roteiro de treino **chainer_mnist.py** já está previsto para si. Na prática, você deve ser capaz de pegar qualquer script de treino personalizado como está e executá-lo com Azure ML sem ter que modificar o seu código.

Para utilizar as capacidades de rastreio e métricas Do Azure ML, adicione uma pequena quantidade de código Azure ML dentro do seu script de treino.  O script de treino **chainer_mnist.py** mostra como registar algumas `Run` métricas na sua execução Azure ML usando o objeto dentro do script.

O script de treino fornecido utiliza `datasets.mnist.get_mnist` dados exemplo sada da função chainer.  Para os seus próprios dados, poderá ter de utilizar passos como [o Upload dataset e scripts](how-to-train-keras.md#data-upload) para disponibilizar dados durante o treino.

Copie o guião de treino **chainer_mnist.py** para o seu diretório de projeto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Criar uma experiência de aprendizagem profunda

Criar uma experimentação. Neste exemplo, crie uma experiência chamada "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Criar ou obter um alvo de computação

Precisas de um [alvo de cálculo](concept-compute-target.md) para treinar o teu modelo. Neste exemplo, utiliza-se a computação gerida azure ML (AmlCompute) para o seu recurso de computação de treino remoto.

**A criação de AmlCompute demora aproximadamente 5 minutos**. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, este código ignora o processo de criação.  

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

Para obter mais informações sobre os alvos da computação, consulte o [que é um artigo-alvo de cálculo.](concept-compute-target.md)

## <a name="create-a-chainer-estimator"></a>Criar um estimador chainer

O [estimador chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornece uma forma simples de lançar trabalhos de formação chainer no seu alvo de computação.

O estimador chainer é implementado [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) através da classe genérica, que pode ser usada para apoiar qualquer quadro. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte modelos de [comboio com Machine Learning Azure utilizando estimador](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após a sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: É criada uma imagem de estivador de acordo com o estimador chainer. A imagem é enviada para o registo de contentores do espaço de trabalho e cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Escalação**: O cluster tenta aumentar a escala se o cluster de IA do lote necessitar de mais nós para executar a execução do que atualmente disponível.

- **Execução**: Todos os scripts da pasta script são enviados para o alvo da computação, as lojas de dados são montadas ou copiadas e a entry_script é executada. As saídas de dstout e a pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorizar o execução.

- **Pós-Processamento**: A pasta ./saídas do executo é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Guardar e registar o modelo

Depois de ter treinado o modelo, pode economizar e registá-lo no seu espaço de trabalho. O registo do modelo permite-lhe armazenar e verver os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação](concept-model-management-and-deployment.md)de modelos.


Depois de concluído o treino do modelo, registe o modelo no seu espaço de trabalho com o seguinte código.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> O modelo que acabou de registar é implementado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador utilizado para treinar. O modo de implementação contém uma secção sobre modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode descarregar uma cópia local do modelo. Isto pode ser útil para fazer trabalhos adicionais de validação de modelos localmente. No roteiro de `chainer_mnist.py`treino, um objeto de poupança persiste o modelo para uma pasta local (local para o alvo da computação). Pode utilizar o objeto Executar para descarregar uma cópia da datastore.

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

Neste artigo, treinou e registou uma rede neural de aprendizagem profunda utilizando Chainer em Azure Machine Learning. Para aprender a implementar um modelo, continue para o nosso artigo de [implementação](how-to-deploy-and-where.md) de modelos.

* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)

* [Veja a nossa arquitetura de referência para formação distribuída de aprendizagem profunda em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
