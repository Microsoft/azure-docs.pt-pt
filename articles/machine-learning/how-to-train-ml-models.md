---
title: Treine modelos ML com estimadores
titleSuffix: Azure Machine Learning
description: Aprenda a realizar um nó único e a formação distribuída de modelos tradicionais de aprendizagem automática e de aprendizagem profunda utilizando a classe Azure Machine Learning Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79078487"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modelos de trem com Machine Learning Azure usando estimador
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, pode facilmente submeter o seu script de treino a [vários alvos de computação,](how-to-set-up-training-targets.md#compute-targets-for-training)utilizando um [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e um [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Esse padrão dá-lhe muita flexibilidade e controlo máximo.

Para facilitar a formação de modelos de aprendizagem profunda, o Azure Machine Learning Python SDK fornece uma abstração alternativa de alto nível, a classe estimador, que permite aos utilizadores construir facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter script de treino usando qualquer quadro de aprendizagem que você escolher (como scikit-learn) em qualquer alvo computacional que você escolher, seja a sua máquina local, um único VM em Azure, ou um cluster GPU em Azure. Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respetivos estimativas [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar a utilização destas estruturas.

## <a name="train-with-an-estimator"></a>Preparar com um avaliador que esteja

Uma vez criado o seu espaço de [trabalho](concept-workspace.md) e configurando o seu ambiente de [desenvolvimento,](how-to-configure-environment.md)treinar um modelo em Azure Machine Learning envolve os seguintes passos:  
1. Crie um [alvo de computação remota](how-to-set-up-training-targets.md) (nota que também pode usar o computador local como alvo de computação)
2. Faça upload dos seus dados de [formação](how-to-access-data.md) para datastore (Opcional)
3. Crie o seu [roteiro de treino](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um objeto `Estimator`
5. Submeta o estimador a um objeto de experiência sob o espaço de trabalho

Este artigo se concentra nas etapas 4 e 5. Para os passos 1-3, consulte o [comboio um tutorial modelo,](tutorial-train-models-with-aml.md) por exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Utilize um `Estimator` para uma corrida de treino de um único nó em computação remota em Azure para um modelo de aprendizagem de ficção. Já deve ter criado o seu [objeto-alvo de cálculo](how-to-set-up-training-targets.md#amlcompute) `compute_target` e o seu objeto [FileDataset](how-to-create-register-datasets.md) `ds`.

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

Este fragmento de código especifica os seguintes parâmetros ao construtor `Estimator`.

Parâmetro | Descrição
--|--
`source_directory`| Diretório de local que contém todos os seus códigos necessários para a tarefa de preparação. Esta pasta é copiada da sua máquina local para a computação remota.
`script_params`| Dicionário especificando os argumentos da linha de comando para passar para o seu roteiro de treino `entry_script`, na forma de pares `<command-line argument, value>`. Para especificar uma bandeira verbosa em `script_params`, use `<command-line argument, "">`.
`compute_target`| O objetivo da computação remota é que o seu script de treino irá funcionar, neste caso um cluster Azure Machine Learning Compute[(AmlCompute).](how-to-set-up-training-targets.md#amlcompute) (Note que, embora o cluster AmlCompute seja o alvo comumente utilizado, também é possível escolher outros tipos de alvo de cálculo, como os VMs Azure ou mesmo o computador local.)
`entry_script`| Filepath (em relação ao `source_directory`) do script de treino a ser executado na computação remota. Este ficheiro, e quaisquer ficheiros adicionais de que dependa, devem estar localizados nesta pasta.
`conda_packages`| Lista de pacotes de Python a serem instalados por meio de conda necessário ao seu script de treinamento.  

O construtor tem outro parâmetro chamado `pip_packages` que você usa para quaisquer pacotes de pip necessários.

Agora que criou o seu `Estimator` objeto, submeta o trabalho de treino a ser executado na computação remota com uma chamada para a função `submit` no seu objeto [experimento](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *saídas* e *registos,* recebem tratamento especial pela Azure Machine Learning. Durante o treino, quando escreve ficheiros para pastas *nomeadas saídas* e *registos relativos* ao diretório raiz (`./outputs` e `./logs`, respectivamente), os ficheiros serão automaticamente enviados para o seu histórico de execução para que tenha acesso a eles assim que a sua execução estiver concluída.
>
> Para criar artefactos durante o treino (como ficheiros de modelos, pontos de verificação, ficheiros de dados ou imagens traçadas) escreva-os na pasta `./outputs`.
>
> Da mesma forma, pode escrever quaisquer registos desde o seu treino até à pasta `./logs`. Para utilizar a [integração tensorBoard](https://aka.ms/aml-notebook-tb) da Azure Machine Learning, certifique-se de que escreve os seus registos TensorBoard para esta pasta. Embora a execução está em curso, poderá iniciar TensorBoard e transmitir em fluxo estes registos.  Mais tarde, também poderá restaurar os registos a partir de qualquer uma das suas execuções anteriores.
>
> Por exemplo, para descarregar um ficheiro escrito na pasta de *saídas* para a sua máquina local após a sua execução de treino remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Formação distribuída e imagens personalizadas do Docker

Existem dois cenários de treino adicionais que pode realizar com o `Estimator`:
* Com uma imagem personalizada do Docker
* Formação distribuída num cluster com vários nó

O código que se segue mostra como realizar treino distribuído para um modelo Keras. Além disso, em vez de utilizar as imagens padrão de Aprendizagem automática de Máquinas Azure, especifica uma imagem personalizada do Docker Hub `continuumio/miniconda` para treino.

Já devia ter criado o seu [objeto-alvo de cálculo](how-to-set-up-training-targets.md#amlcompute) `compute_target`. Criar o avaliador da seguinte forma:

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

O código acima expõe os seguintes novos parâmetros ao construtor `Estimator`:

Parâmetro | Descrição | Predefinição
--|--|--
`custom_docker_image`| Nome da imagem que pretende utilizar. Forneça apenas as imagens disponíveis nos repositórios de docker público (no caso este Docker Hub). Para utilizar uma imagem de um repositório privado de estivadores, utilize o parâmetro de `environment_definition` do construtor. [Ver exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Número de nós a utilizar para a tarefa de preparação. | `1`
`process_count_per_node`| Número de processos (ou "funcionários") para executar em cada nó. Neste caso, utiliza as GPUs `2` disponíveis em cada nó.| `1`
`distributed_training`| [Objeto DE CONFIGURAÇÃO MPI](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) Para lançamento de treino distribuído utilizando o backend MPI.  | `None`


Por fim, submeta a tarefa de preparação:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registar um modelo

Depois de ter treinado o modelo, pode economizar e registá-lo no seu espaço de trabalho. O registo do modelo permite-lhe armazenar e verver os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação](concept-model-management-and-deployment.md)de modelos.

A execução do seguinte código registará o modelo no seu espaço de trabalho e disponibilizá-lo-á para referência por nome em contextos de computação remota ou scripts de implementação. Consulte [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) nos médicos de referência para obter mais informações e parâmetros adicionais.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Rastreio e integração gitHub

Quando se inicia uma corrida de formação onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para mais informações, consulte a [integração de Git para Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="examples"></a>Exemplos
Para um caderno que mostre o básico de um padrão estimador, consulte:
* [como-usar-azureml/formação-com-aprendizagem profunda/como-usar-estimador](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Para um caderno que treina um modelo de aprendizagem de ficção através do estimador, consulte:
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Para os cadernos sobre modelos de formação utilizando estimativas específicas de quadro profundo de aprendizagem, consulte:

* [como-usar-azureml/ml-quadros](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Modelos PyTorch de trem](how-to-train-pytorch.md)
* [Modelos TensorFlow do comboio](how-to-train-tensorflow.md)
* [Hiperparâmetros de melodia](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Criar e gerir ambientes para formação e implantação](how-to-use-environments.md)