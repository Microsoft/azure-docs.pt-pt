---
title: Treinar modelos ML com estimadores
titleSuffix: Azure Machine Learning
description: Saiba como executar um único nó e treinamento distribuído de aprendizado de máquina tradicional e modelos de aprendizado profundo usando Azure Machine Learning classe de estimador
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: ce1076446fb704bb64bac98c7afe53e63d3b3450
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912427"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Treinar modelos com Azure Machine Learning usando o estimador
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, você pode enviar facilmente seu script de treinamento para [vários destinos de computação](how-to-set-up-training-targets.md#compute-targets-for-training), usando um [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e um [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Esse padrão oferece muita flexibilidade e controle máximo.

Para facilitar o treinamento do modelo de aprendizado profundo, o SDK do Python Azure Machine Learning fornece uma abstração alternativa de nível superior, a classe estimadora, que permite aos usuários construir facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar o script de treinamento usando qualquer estrutura de aprendizado que escolher (como scikit-learn) em qualquer destino de computação escolhido, seja seu computador local, uma única VM no Azure ou um cluster de GPU no Azure. Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

## <a name="train-with-an-estimator"></a>Preparar com um avaliador que esteja

Depois de criar a sua [área de trabalho](concept-workspace.md) e configure a sua [ambiente de desenvolvimento](how-to-configure-environment.md), preparar um modelo no Azure Machine Learning envolve os seguintes passos:  
1. Criar um [destino de computação remota](how-to-set-up-training-targets.md) (Observação Você também pode usar o computador local como destino de computação)
2. Carregar seus [dados de treinamento](how-to-access-data.md) no repositório de armazenamento (opcional)
3. Criar sua [script de treinamento](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um `Estimator` objeto
5. Enviar o estimador para um objeto de experimento no espaço de trabalho

Este artigo se concentra nas etapas 4 e 5. Para obter passos 1 a 3, veja a [treinar um tutorial de modelo](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Utilize um `Estimator` para um treinamento de nó único executar no cálculo remoto no Azure para um scikit-saiba modelo. Deverá ter já criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target` e a sua [arquivo de dados](how-to-access-data.md) objeto `ds`.

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

Este fragmento de código especifica os parâmetros seguintes para o `Estimator` construtor.

Parâmetro | Descrição
--|--
`source_directory`| Diretório de local que contém todos os seus códigos necessários para a tarefa de preparação. Essa pasta é copiada do computador local para a computação remota.
`script_params`| Dicionário que especifica os argumentos de linha de comando a serem passados para o script de treinamento `entry_script`, na forma de pares de `<command-line argument, value>`. Para especificar um sinalizador detalhado em `script_params`, use `<command-line argument, "">`.
`compute_target`| O destino de computação remota no qual o script de treinamento será executado, nesse caso, um cluster Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Observe que, embora o cluster AmlCompute seja o destino comumente usado, também é possível escolher outros tipos de destino de computação, como VMs do Azure ou mesmo computador local.)
`entry_script`| Caminho do ficheiro (relativa a `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e os arquivos adicionais dos quais ele depende devem estar localizados nessa pasta.
`conda_packages`| Lista de pacotes de Python a serem instalados por meio de conda necessário ao seu script de treinamento.  

O construtor tem outro parâmetro chamado `pip_packages` que você usa para qualquer pacote de Pip necessário.

Agora que criou sua `Estimator` objeto, submeter a tarefa de preparação para ser executado na computação remota com uma chamada para o `submit` funcionar no seu [experimentação](concept-azure-machine-learning-architecture.md#experiments) objeto `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas especiais** duas pastas, *produz* e *registos*, receber tratamento especial do Azure Machine Learning. Durante o treinamento, quando gravar arquivos em pastas denominadas *produz* e *registos* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os ficheiros serão automaticamente carregar para o seu histórico de execução para que tenha acesso aos mesmos, uma vez concluída a execução.
>
> Para criar artefactos durante o treinamento (por exemplo, ficheiros de modelo, os pontos de verificação, arquivos de dados ou imagens plotadas) para escrever o `./outputs` pasta.
>
> Da mesma forma, você pode gravar quaisquer logs de seu treinamento executados na pasta `./logs`. Para utilizar o Azure Machine Learning [TensorBoard integração](https://aka.ms/aml-notebook-tb) certificar-se de que escreve os registos de TensorBoard para esta pasta. Embora a execução está em curso, poderá iniciar TensorBoard e transmitir em fluxo estes registos.  Mais tarde, também poderá restaurar os registos a partir de qualquer uma das suas execuções anteriores.
>
> Por exemplo, para transferir um ficheiro escrito para o *produz* pasta no seu computador local após seu treinamento remoto, execute: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Formação distribuída e imagens personalizadas do Docker

Existem dois cenários de formação adicionais que podem ser executadas com o `Estimator`:
* Com uma imagem personalizada do Docker
* Formação distribuída num cluster com vários nó

O código a seguir mostra como realizar o treinamento distribuído para um modelo Keras. Além disso, em vez de usar as imagens de Azure Machine Learning padrão, ele especifica uma imagem personalizada do Docker do Hub do Docker `continuumio/miniconda` para treinamento.

Deverá ter já criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target`. Criar o avaliador da seguinte forma:

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

O código acima expõe os seguintes novos parâmetros para o `Estimator` construtor:

Parâmetro | Descrição | Predefinição
--|--|--
`custom_docker_image`| Nome da imagem que pretende utilizar. Forneça apenas as imagens disponíveis nos repositórios de docker público (no caso este Docker Hub). Para usar uma imagem de um repositório do Docker privado, use o parâmetro `environment_definition` do Construtor em vez disso. [Consulte o exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Número de nós a utilizar para a tarefa de preparação. | `1`
`process_count_per_node`| Número de processos (ou "funcionários") para executar em cada nó. Neste caso, utilize o `2` GPUs disponíveis em cada nó.| `1`
`distributed_training`| Objeto [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para iniciar o treinamento distribuído usando o back-end MPI.  | `None`


Por fim, submeta a tarefa de preparação:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrando um modelo

Depois de treinar o modelo, você poderá salvá-lo e registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

Executar o código a seguir registrará o modelo em seu espaço de trabalho e o disponibilizará para referência por nome em contextos de computação remota ou scripts de implantação. Consulte [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) nos docs de referência para obter mais informações e parâmetros adicionais.

```python
model = run.register_model(model_name='sklearn-sample')
```

## <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, consulte [integração do git para Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exemplos
Para um notebook que mostra as noções básicas de um padrão estimador, consulte:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Para um bloco de anotações que treina um modelo scikit usando o estimador, consulte:
* [tutoriais/img-classificação-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Para blocos de anotações em modelos de treinamento usando avaliadores específicos da estrutura de aprendizado profundo, consulte:

* [instruções-uso do-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Utilizar modelos de PyTorch](how-to-train-pytorch.md)
* [Utilizar modelos do TensorFlow](how-to-train-tensorflow.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
