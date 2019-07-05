---
title: Modelos de Chainer comboio e registre-se
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como dar formação e registar um modelo de Chainer usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488660"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar modelos Chainer à escala com o serviço Azure Machine Learning

Este artigo mostra-lhe como dar formação e registar um modelo de Chainer usando o serviço Azure Machine Learning. Ele usa a popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) classificar manuscritos dígitos a utilizar uma rede neural profunda (DNN) criada com o [biblioteca de Python de Chainer](https://Chainer.org) em execução na parte superior do [numpy](https://www.numpy.org/).

Chainer é uma API com capacidade para executar na parte superior de outras estruturas DNN populares para simplificar o desenvolvimento de rede neural alto nível. Com o serviço do Azure Machine Learning, pode rapidamente aumentar horizontalmente trabalhos de treinamento com recursos de computação na cloud elástica. Também pode controlar a execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo de Chainer desde o início ou trazem um modelo existente para a cloud, serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um destes ambientes:

- Azure Machine Learning bloco de notas do VM - nenhuma downloads ou de uma instalação necessária

    - Concluir o [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md) para criar um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo.
    - Na pasta de exemplos no servidor do bloco de notas, encontrar um bloco de notas concluído e ficheiros a **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** pasta.  O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente, a implementação de modelo e widgets de bloco de notas.

- O seu servidor de bloco de notas do Jupyter

    - [Instalar o Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um ficheiro de configuração da área de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - Transfira o ficheiro de script de exemplo [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) deste guia na página de exemplos do GitHub. O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente, a implementação de modelo e widgets de bloco de notas.

## <a name="set-up-the-experiment"></a>Configurar a experimentação

Esta secção define a experimentação de preparação por carregar os pacotes python necessários, a inicializar uma área de trabalho, criação de uma experimentação e carregar os dados de treinamento e os scripts de preparação.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe o azureml.core exibição de anúncio de biblioteca de Python o número de versão.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializar uma área de trabalho

O [área de trabalho do Azure Machine Learning serviço](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefactos de que criar. O SDK de Python, pode acessar os artefactos de área de trabalho através da criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Criar um objeto de área de trabalho a partir da `config.json` ficheiro criado no [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Criar um diretório do projeto
Crie um diretório que contém o código necessário do seu computador local que vai precisar de acesso para o recurso remoto. Isto inclui o script de treinamento e arquivos adicionais que depende do seu script de treinamento.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparar o script de treinamento

Neste tutorial, o script de formação **chainer_mnist.py** já é fornecido por si. Na prática, deve ser capazes de tirar qualquer script de treinamento personalizado que esteja e executá-lo com o Azure ML sem ter de modificar o seu código.

Para utilizar o controlo e capacidades de métricas do Azure ML, terá de adicionar uma pequena quantidade de código do Azure ML dentro do script de treinamento.  O script de formação **chainer_mnist.py** mostra como iniciar sessão algumas métricas para o Azure ML executar. Para fazer isso, acessa o Azure ML `Run` objeto no script.

Copie o script de formação **chainer_mnist.py** no seu diretório do projeto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "chainer mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Criar ou obter um destino de computação

Será necessário um [destino de computação](concept-compute-target.md) para seu modelo de formação. Neste tutorial, irá utilizar a computação do Azure ML geridos (AmlCompute) para o seu recurso de computação de treinamento remoto.

**Criação de AmlCompute demora, aproximadamente, 5 minutos**. Se o AmlCompute com esse nome já está em sua área de trabalho, esse código irá ignorar o processo de criação.  

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

Para obter mais informações sobre os destinos de computação, consulte a [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-chainer-estimator"></a>Criar uma calculadora de Chainer

O [estimador de Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornece uma forma simples de iniciar as tarefas de formação do Chainer no destino de computação.

O avaliador de Chainer é implementado por meio de genérica [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser utilizado para suportar qualquer estrutura. Para obter mais informações sobre modelos com o avaliador de genérico de treinamento, consulte [formar modelos com o Azure Machine Learning utilizando estimator](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Submeter uma execução

O [executar o objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execuções, enquanto a tarefa está em execução e depois de concluída.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Como a execução é executada, executa as seguintes fases:

- **A preparar**: Uma imagem do docker é criada, de acordo com o avaliador de Chainer. A imagem é carregada para container registry a área de trabalho e armazenada em cache para execuções posteriores. Os registos também são transmitidos para o histórico de execuções e podem ser visualizados para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar verticalmente, se o cluster do Batch AI requer mais nós para executar a execução que estão atualmente disponíveis.

- **Executar**: Todos os scripts na pasta de script são carregados para o destino de computação, arquivos de dados são montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs são transmitidas em fluxo para o histórico de execuções e pode ser utilizado para monitorizar a execução.

- **Pós-processamento**: A. / produz a pasta da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Guardar e registe o modelo

Depois de ter preparado o modelo, pode guardar e registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

Adicione o seguinte código ao seu script de formação **chainer_mnist.py**, para guardar o modelo. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Registe o modelo para sua área de trabalho com o código a seguir.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Passos Seguintes

Neste artigo, preparado um modelo de Chainer no serviço Azure Machine Learning. 

* Para saber como implementar um modelo, continue para nosso [implementação de modelo](how-to-deploy-and-where.md) artigo.

* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)

* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
