---
title: Utilizar modelos com o TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Saiba como executar o nó único e distribuída formação de TensorFlow e Keras modelos com os TensorFlow e Keras estimadores
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/21/2019
ms.custom: seodec18
ms.openlocfilehash: b06151b9cb5603ef54c8e84d261bb7a4e7885e1b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880466"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Dar formação de TensorFlow e Keras modelos com o serviço Azure Machine Learning

Para aprender de rede neurais profundas (DNN) com o TensorFlow, o Azure Machine Learning fornece um personalizado `TensorFlow` classe do `Estimator`. O Azure SDK `TensorFlow` estimator (para não ser conflated com o [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) classe) permite-lhe submeter facilmente tarefas de formação de TensorFlow para execuções de nó único e distribuídas na computação do Azure.

## <a name="single-node-training"></a>Treinamento de nó único
Treinamento com o `TensorFlow` estimator é semelhante à utilização do [base `Estimator` ](how-to-train-ml-models.md), leia o artigo que mostra como primeiro e certifique-se de que compreende os conceitos apresentados aqui.
  
Para executar uma tarefa do TensorFlow, instanciar um `TensorFlow` objeto. Deverá ter já criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Aqui, podemos especificar os parâmetros seguintes para o construtor do TensorFlow:

Parâmetro | Descrição
--|--
`source_directory` | Diretório de local que contém todos os seus códigos necessários para a tarefa de preparação. Esta pasta é copiada a partir do seu computador local para a computação remota
`script_params` | Especificar os argumentos da linha de comandos para o script de treinamento de dicionário `entry_script`, na forma de < argumento da linha de comandos, valor > pares
`compute_target` | Destino de computação remota que o script de treinamento serão executados no, neste caso uma computação do Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) cluster
`entry_script` | Caminho do ficheiro (relativa a `source_directory`) do script de treinamento para ser executado na computação remota. Este ficheiro e todos os arquivos adicionais depende, devem ser localizados na pasta
`conda_packages` | Lista de pacotes de Python a serem instalados por meio de conda necessário ao seu script de treinamento. Nesse caso, o script de treinamento usa `sklearn` para carregar os dados, pelo que deve especificar esse pacote a ser instalado.  O construtor tem outro parâmetro chamado `pip_packages` que pode utilizar quaisquer pacotes de pip necessário
`use_gpu` | Definir este sinalizador `True` para tirar partido de GPU para treinamento. Assume a predefinição `False`.

Uma vez que estiver a utilizar o avaliador de TensorFlow, o contentor usado para treinamento usará como padrão incluem o pacote de TensorFlow e dependências relacionadas necessárias para treinamento em CPUs e GPUs.

Em seguida, submeta a tarefa do TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Suporte Keras
[Keras](https://keras.io/) é uma API de Python de DNN de alto nível populares que suporta o TensorFlow, CNTK ou Theano como back-ends. Se usar o TensorFlow como back-end, pode facilmente usar o avaliador de TensFlow para preparar um modelo do Keras. Eis um exemplo de um estimador de TensorFlow com Keras adicionada a ele:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
O construtor de estimador de TensorFlow acima indica ao serviço Azure Machine Learning para instalar Keras através do pip para o ambiente de execução. E sua `keras_train.py` , em seguida, pode importar a API Keras para preparar um modelo do Keras. Para obter um exemplo completo, explore [este bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

## <a name="distributed-training"></a>Formação distribuída
O avaliador de TensorFlow também permite-lhe preparar seus modelos à escala em clusters GPU e CPU de VMs do Azure. Pode facilmente executar formação de TensorFlow distribuída com algumas chamadas de API, enquanto o Azure Machine Learning gerenciará em segundo plano, a infraestrutura e o necessário para executar estas cargas de trabalho de orquestração.

O Azure Machine Learning suporta dois métodos de formação distribuída no TensorFlow:
* Com base em MPI distribuídas de treinamento com o [Horovod](https://github.com/uber/horovod) framework
* nativo [distribuídas do TensorFlow](https://www.tensorflow.org/deploy/distributed) por meio do método de servidor de parâmetro

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é uma arquitetura de código-fonte aberto em anel-allreduce para treinamento distribuído desenvolvidos por Uber.

Para executar o TensorFlow distribuída com o framework Horovod, crie o objeto do TensorFlow da seguinte forma:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

O código acima expõe os seguintes novos parâmetros para o construtor do TensorFlow:

Parâmetro | Descrição | Predefinição
--|--|--
`node_count` | Número de nós a utilizar para a tarefa de preparação. | `1`
`process_count_per_node` | Número de processos (ou "funcionários") para executar em cada nó.|`1`
`distributed_backend` | Back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI. Se deseja realizar treinamento paralelo ou distribuído (por exemplo, `node_count`> 1 ou `process_count_per_node`> 1 ou ambos) com MPI (e Horovod), defina `distributed_backend='mpi'`. A implementação de MPI utilizada pelo Azure Machine Learning é [MPI aberto](https://www.open-mpi.org/). | `None`

O exemplo acima executará formação distribuída com duas funções de trabalho, um operador por nó.

Horovod e as respetivas dependências serão instaladas para, portanto, pode importá-lo no seu script de treinamento `train.py` da seguinte forma:

```Python
import tensorflow as tf
import horovod
```

Por fim, submeta a tarefa do TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Servidor de parâmetros
Também pode executar [nativo distribuído TensorFlow](https://www.tensorflow.org/deploy/distributed), que utiliza o modelo de parâmetro de servidor. Nesse método, Treine num cluster de servidores de parâmetro e funções de trabalho. Os operadores de calculam os gradientes durante o treinamento, enquanto os servidores de parâmetro agregam os gradientes.

Construa o objeto do TensorFlow:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Preste atenção para os parâmetros seguintes para o construtor do TensorFlow no código anterior:

Parâmetro | Descrição | Predefinição
--|--|--
`worker_count` | Número de workers. | `1`
`parameter_server_count` | Número de servidores de parâmetro. | `1`
`distributed_backend` | Back-end a utilizar para formação distribuída. Para fazer o treinamento distribuído por meio do servidor de parâmetros, defina `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>Tenha em atenção no `TF_CONFIG`
Também terá dos endereços de rede e portas do cluster para o [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), por isso, o Azure Machine Learning define o `TF_CONFIG` variável de ambiente para.

O `TF_CONFIG` variável de ambiente é uma cadeia de caracteres do JSON. Eis um exemplo da variável para um servidor de parâmetro:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Se estiver a utilizar o nível mais alto do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analisar isso `TF_CONFIG` variável e o cluster de compilação especificar para. 

Se estiver a utilizar em vez disso, APIs de principais de nível inferior do TensorFlow para treinamento, deve analisar os `TF_CONFIG` variável e compilação o `tf.train.ClusterSpec` -se no seu código de treinamento. Na [neste exemplo](https://aka.ms/aml-notebook-tf-ps), faria, neste **seu script de treinamento** da seguinte forma:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Quando terminar de escrever o script de treinamento e criar o objeto do TensorFlow, pode submeter a tarefa de preparação:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Exemplos

Explore várias [blocos de notas na aprendizagem profunda distribuída no Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
