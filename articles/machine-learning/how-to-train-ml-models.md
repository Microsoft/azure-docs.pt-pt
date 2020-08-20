---
title: Modelos ML de comboio com estimadores
titleSuffix: Azure Machine Learning
description: Aprenda a realizar um único nó e treino distribuído de modelos tradicionais de aprendizagem automática e aprendizagem profunda usando a classe Azure Machine Learning Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: fe7210ad52c756f140144f04e3b747c0bfcd00c3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650320"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modelos de trem com Azure Machine Learning usando estimador
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com a Azure Machine Learning, pode facilmente submeter o seu script de treino a [vários alvos de computação,](how-to-set-up-training-targets.md#compute-targets-for-training)utilizando um [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e um [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Este padrão dá-lhe muita flexibilidade e controlo máximo.


A classe de estimador facilita a formação de modelos com aprendizagem profunda e aprendizagem de reforço. Proporciona uma abstração de alto nível que permite construir facilmente a configuração de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter script de formação usando qualquer enquadramento de aprendizagem que você escolher (como scikit-learn) em qualquer alvo de computação que você escolher, seja a sua máquina local, um único VM em Azure, ou um cluster GPU em Azure. Para tarefas de aprendizagem de PyTorch, TensorFlow, Chainer e reforço, a Azure Machine Learning também fornece os respetivos [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)e estimativas de [aprendizagem de reforço](how-to-use-reinforcement-learning.md) para simplificar usando estes quadros.

## <a name="train-with-an-estimator"></a>Comboio com um estimador

Uma vez criado o seu [espaço de trabalho](concept-workspace.md) e configurando o seu ambiente de [desenvolvimento,](how-to-configure-environment.md)a formação de um modelo em Azure Machine Learning envolve os seguintes passos:  
1. Crie um [alvo de computação remota](how-to-set-up-training-targets.md) (nota que também pode usar o computador local como alvo de computação)
2. Faça o upload dos seus [dados de formação](how-to-access-data.md) para datastore (Opcional)
3. Crie o seu [roteiro de treino](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um objeto `Estimator`
5. Submeta o estimador a um objeto de experiência no espaço de trabalho

Este artigo centra-se nos passos 4-5. Para os passos 1-3, consulte o [comboio como tutorial de modelo,](tutorial-train-models-with-aml.md) por exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Use um `Estimator` para um treino de nó único executado em computação remota em Azure para um modelo de aprendizagem de scikit. Já deveria ter criado o seu [objeto-alvo de computação](how-to-set-up-training-targets.md#amlcompute) `compute_target` e o seu objeto [FileDataset](how-to-create-register-datasets.md) `ds` .

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Este corte de código especifica os seguintes parâmetros para o `Estimator` construtor.

Parâmetro | Descrição
--|--
`source_directory`| Diretório local que contém todo o seu código necessário para o trabalho de treino. Esta pasta é copiada da sua máquina local para o cálculo remoto.
`script_params`| Dicionário especificando os argumentos da linha de comando para passar para o seu script `entry_script` de treino, na forma de `<command-line argument, value>` pares. Para especificar uma bandeira verbosa `script_params` em , utilize `<command-line argument, "">` .
`compute_target`| Alvo de computação remota que o seu script de treino irá funcionar, neste caso um cluster Azure Machine Learning Compute[(AmlCompute).](how-to-set-up-training-targets.md#amlcompute) (Note que, embora o cluster AmlCompute seja o alvo comumente utilizado, também é possível escolher outros tipos-alvo de computação, tais como VMs Azure ou mesmo computador local.)
`entry_script`| Filepath (relativamente ao `source_directory` ) do script de treino a ser executado no cálculo remoto. Este ficheiro, e quaisquer ficheiros adicionais de que dependa, devem ser localizados nesta pasta.
`conda_packages`| Lista de pacotes Python a instalar via conda necessária pelo seu script de treino.  

O construtor tem outro parâmetro chamado `pip_packages` que você usa para quaisquer pacotes de pip necessários.

Agora que criou o seu `Estimator` objeto, submeta o trabalho de treino para ser executado no cálculo remoto com uma chamada para a `submit` função no seu objeto [Experimento](concept-azure-machine-learning-architecture.md#experiments) `experiment` . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas especiais** Duas *pastas, saídas* e *troncos,* recebem tratamento especial pela Azure Machine Learning. Durante o treino, quando escreve ficheiros para *pastas nomeadas saídas* e *registos relativos* ao diretório de raiz `./outputs` `./logs` (e, respectivamente), os ficheiros serão automaticamente carregados para o seu histórico de execução para que tenha acesso aos mesmos uma vez que a sua execução esteja terminada.
>
> Para criar artefactos durante o treino (como ficheiros de modelos, pontos de verificação, ficheiros de dados ou imagens traçadas) escreva-os para a `./outputs` pasta.
>
> Da mesma forma, pode escrever quaisquer registos da sua corrida de treino para a `./logs` pasta. Para utilizar a integração do [TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) da Azure Machine Learning certifique-se de que escreve os seus registos TensorBoard nesta pasta. Enquanto a sua execução estiver em andamento, poderá lançar o TensorBoard e transmitir estes registos.  Mais tarde, também poderá restaurar os registos de qualquer uma das suas execuções anteriores.
>
> Por exemplo, para descarregar um ficheiro escrito para a pasta *de saídas* para a sua máquina local após o seu treino remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treino distribuído e imagens personalizadas do Docker

Existem dois cenários de formação adicionais que pode realizar `Estimator` com:
* Usando uma imagem personalizada do Docker
* Treino distribuído num cluster multi-nos

O código que se segue mostra como realizar treinos distribuídos para um modelo Keras. Além disso, em vez de usar as imagens padrão de Azure Machine Learning, especifica uma imagem personalizada do Docker Hub `continuumio/miniconda` para treino.

Já devia ter criado o seu [objeto-alvo de computação.](how-to-set-up-training-targets.md#amlcompute) `compute_target` Cria-se o estimador da seguinte forma:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

O código acima expõe ao construtor os seguintes novos `Estimator` parâmetros:

Parâmetro | Descrição | Predefinição
--|--|--
`custom_docker_image`| Nome da imagem que pretende usar. Apenas forneça imagens disponíveis em repositórios públicos de estivadores (neste caso, Docker Hub). Para utilizar uma imagem de um repositório privado de estivador, use o parâmetro do `environment_definition` construtor.| `None`
`node_count`| Número de nós para usar para o seu trabalho de treino. | `1`
`process_count_per_node`| Número de processos (ou "trabalhadores") para executar em cada nó. Neste caso, utilize as `2` GPUs disponíveis em cada nó.| `1`
`distributed_training`| [Objeto MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para lançamento de formação distribuída usando backend MPI.  | `None`


Por fim, submeta o trabalho de formação:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registar um modelo

Uma vez treinado o modelo, pode economize e registe no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

Executar o seguinte código registará o modelo no seu espaço de trabalho e irá disponibilizá-lo para referência pelo nome em contextos de computação remota ou scripts de implementação. Consulte [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) nos documentos de referência para obter mais informações e parâmetros adicionais.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Rastreio e integração do GitHub

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="examples"></a>Exemplos

Para um caderno que treina um modelo de aprendizagem de scikit utilizando o estimador, consulte:
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

Para cadernos sobre modelos de formação utilizando estimativas específicas de quadro de aprendizagem profunda, consulte:

* [como usar-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Preparar modelos de PyTorch](how-to-train-pytorch.md)
* [Preparar modelos de TensorFlow](how-to-train-tensorflow.md)
* [Treine um reforço aprendendo rede neural profunda](how-to-use-reinforcement-learning.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Criar e gerir ambientes para formação e implantação](how-to-use-environments.md)