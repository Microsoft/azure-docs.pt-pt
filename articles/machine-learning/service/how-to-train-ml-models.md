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
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 087e1cd84aa182a0aae1bef6ba3dd38f369d5189
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755945"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Treinar modelos com Azure Machine Learning usando o estimador

Com o Azure Machine Learning, você pode enviar facilmente seu script de treinamento para [vários destinos de computação](how-to-set-up-training-targets.md#compute-targets-for-training), usando o [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e o [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Esse padrão oferece muita flexibilidade e controle máximo.

Para facilitar o treinamento do modelo de aprendizado profundo, o SDK do Python Azure Machine Learning fornece uma abstração alternativa de nível superior, a classe estimadora, que permite aos usuários construir facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar o script de treinamento usando qualquer estrutura de aprendizado que escolher (como scikit-learn) em qualquer destino de computação escolhido, seja seu computador local, uma única VM no Azure ou um cluster de GPU no Azure. Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

## <a name="train-with-an-estimator"></a>Treinar com um estimador

Depois de criar seu [espaço de trabalho](concept-workspace.md) e configurar seu [ambiente de desenvolvimento](how-to-configure-environment.md), treinar um modelo no Azure Machine Learning envolve as seguintes etapas:  
1. Criar um [destino de computação remota](how-to-set-up-training-targets.md) (Observação Você também pode usar o computador local como destino de computação)
2. Carregar seus [dados de treinamento](how-to-access-data.md) no repositório de armazenamento (opcional)
3. Criar seu [script de treinamento](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um objeto `Estimator`
5. Enviar o estimador para um objeto de experimento no espaço de trabalho

Este artigo se concentra nas etapas 4-5. Para obter as etapas de 1-3, consulte o [tutorial treinar um modelo](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Use um `Estimator` para um treinamento de nó único executado na computação remota no Azure para um modelo scikit-learn. Você já deve ter criado o objeto de [destino de computação](how-to-set-up-training-targets.md#amlcompute) `compute_target` e seu objeto de [repositório de armazenamento](how-to-access-data.md) `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Este trecho de código especifica os parâmetros a seguir para o construtor de `Estimator`.

Parâmetro | Descrição
--|--
`source_directory`| Diretório local que contém todo o seu código necessário para o trabalho de treinamento. Essa pasta é copiada do computador local para a computação remota.
`script_params`| Dicionário que especifica os argumentos de linha de comando a serem passados para o script de treinamento `entry_script`, na forma de pares de `<command-line argument, value>`. Para especificar um sinalizador detalhado em `script_params`, use `<command-line argument, "">`.
`compute_target`| O destino de computação remota no qual o script de treinamento será executado, nesse caso, um cluster Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Observe que, embora o cluster AmlCompute seja o destino comumente usado, também é possível escolher outros tipos de destino de computação, como VMs do Azure ou mesmo computador local.)
`entry_script`| FilePath (relativo ao `source_directory`) do script de treinamento a ser executado na computação remota. Esse arquivo e os arquivos adicionais dos quais ele depende devem estar localizados nessa pasta.
`conda_packages`| Lista de pacotes do Python a serem instalados por meio do Conda necessário para seu script de treinamento.  

O construtor tem outro parâmetro chamado `pip_packages` que você usa para qualquer pacote de Pip necessário.

Agora que você criou seu objeto `Estimator`, envie o trabalho de treinamento para ser executado na computação remota com uma chamada para a função `submit` em seu objeto de [experimento](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas especiais** Duas pastas, *saídas* e *logs*, recebem tratamento especial por Azure Machine Learning. Durante o treinamento, ao gravar arquivos em pastas chamadas de *saídas* e *logs* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os arquivos serão carregados automaticamente no seu histórico de execução para que você tenha acesso a eles uma vez sua execução foi concluída.
>
> Para criar artefatos durante o treinamento (como arquivos de modelo, pontos de verificação, arquivos de dados ou imagens plotadas), grave-os na pasta `./outputs`.
>
> Da mesma forma, você pode gravar quaisquer logs de seu treinamento executados na pasta `./logs`. Para utilizar a [integração do TensorBoard](https://aka.ms/aml-notebook-tb) do Azure Machine Learning, certifique-se de escrever os logs do TensorBoard nessa pasta. Enquanto sua execução estiver em andamento, você poderá iniciar o TensorBoard e transmitir esses logs.  Posteriormente, você também poderá restaurar os logs de qualquer uma das suas execuções anteriores.
>
> Por exemplo, para baixar um arquivo gravado na pasta *Outputs* em seu computador local após a execução do treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treinamento distribuído e imagens personalizadas do Docker

Há dois cenários de treinamento adicionais que você pode realizar com o `Estimator`:
* Usando uma imagem personalizada do Docker
* Treinamento distribuído em um cluster de vários nós

O código a seguir mostra como realizar o treinamento distribuído para um modelo Keras. Além disso, em vez de usar as imagens de Azure Machine Learning padrão, ele especifica uma imagem personalizada do Docker do Hub do Docker `continuumio/miniconda` para treinamento.

Você já deve ter criado o objeto de [destino de computação](how-to-set-up-training-targets.md#amlcompute) `compute_target`. Você cria o estimador da seguinte maneira:

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

O código acima expõe os seguintes novos parâmetros para o construtor de `Estimator`:

Parâmetro | Descrição | Predefinição
--|--|--
`custom_docker_image`| Nome da imagem que você deseja usar. Forneça apenas imagens disponíveis em repositórios públicos do Docker (nesse caso, o Hub do Docker). Para usar uma imagem de um repositório do Docker privado, use o parâmetro `environment_definition` do Construtor em vez disso. [Consulte o exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Número de nós a serem usados para seu trabalho de treinamento. | `1`
`process_count_per_node`| Número de processos (ou "trabalhadores") a serem executados em cada nó. Nesse caso, você usa as `2` GPUs disponíveis em cada nó.| `1`
`distributed_training`| Objeto [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para iniciar o treinamento distribuído usando o back-end MPI.  | `None`


Por fim, envie o trabalho de treinamento:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, consulte [integração do git para Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exemplos
Para um notebook que mostra as noções básicas de um padrão estimador, consulte:
* [como usar-azureml/treinamento-com-Deep-Learning/como usar-estimador](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Para um bloco de anotações que treina um modelo scikit usando o estimador, consulte:
* [TUTORIAIS/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Para blocos de anotações em modelos de treinamento usando avaliadores específicos da estrutura de aprendizado profundo, consulte:
* [como usar – azureml/treinamento-com o aprendizado profundo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* [Rastrear métricas de execução durante o treinamento](how-to-track-experiments.md)
* [Treinar modelos do PyTorch](how-to-train-pytorch.md)
* [Treinar modelos do TensorFlow](how-to-train-tensorflow.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
