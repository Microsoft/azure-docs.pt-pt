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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: c8865c851f394d73b5446ac159b5a7799c0c9ed2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192353"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Dar formação de TensorFlow e Keras modelos com o serviço Azure Machine Learning

Pode facilmente executar tarefas de formação de TensorFlow na computação do Azure utilizando o [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) classe estimator no SDK do Azure Machine Learning. O `TensorFlow` estimator direciona o serviço Azure Machine Learning para executar o seu trabalho num contêiner TensorFlow habilitado para treinamento de rede Neural profunda (DNN).

O `TensorFlow` estimator também fornece uma camada de abstração sobre a execução, o que significa que pode configurar facilmente execuções parametrizadas destinos de computação diferentes sem alterar seus scripts de treinamento.

## <a name="getting-started"></a>Introdução

Submeter tarefas com o `TensorFlow` estimator é semelhante à utilização da base de [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py). Portanto, recomendamos que comece por ler o [artigo que mostra como base Estimator](how-to-train-ml-models.md) para compreender os conceitos de abrangente pela primeira vez.

Se pretender começar com o serviço Azure Machine Learning [concluir o guia de introdução](quickstart-run-cloud-notebook.md). Terá um ambiente de trabalho carregá-lo com todos os nossos [blocos de notas de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml).

## <a name="single-node-training"></a>Treinamento de nó único

Para executar uma tarefa do TensorFlow, instanciar um [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) de objeto e submetê-lo como uma experimentação.

O código a seguir cria uma instância de um estimador de TensorFlow e submete-a como uma experimentação. O script de treinamento `train.py` será executada utilizando os parâmetros de script específico. A tarefa será executada num ativadas para GPU [destino de computação](how-to-set-up-training-targets.md)e scikit-saiba será instalado como uma dependência de `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Formação distribuída

O [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator também suporta a formação distribuída em clusters GPU e CPU. Pode facilmente executar tarefas de TensorFlow distribuídas e o serviço Azure Machine Learning irão gerir a infraestrutura e orquestração para.

O serviço de Machine Learning do Azure suporta dois métodos de formação distribuída no TensorFlow:

* [Com base em MPI](https://www.open-mpi.org/) distribuído treinamento usando o [Horovod](https://github.com/uber/horovod) framework
* Nativo [distribuídas do TensorFlow](https://www.tensorflow.org/deploy/distributed) usando o método de servidor de parâmetro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) é uma arquitetura de código-fonte aberto para treinamento distribuído desenvolvidos por Uber. Ele oferece uma forma fácil de tarefas de GPU TensorFlow distribuídas.

O exemplo a seguir executa uma tarefa distribuídas com Horovod com duas funções de trabalho distribuídas entre dois nós.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod e as respetivas dependências serão instaladas para, portanto, pode importá-lo no seu script de treinamento.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Servidor de parâmetros

Também pode executar [nativo distribuído TensorFlow](https://www.tensorflow.org/deploy/distributed), que utiliza o modelo de parâmetro de servidor. Nesse método, Treine num cluster de servidores de parâmetro e funções de trabalho. Os operadores de calculam os gradientes durante o treinamento, enquanto os servidores de parâmetro agregam os gradientes.

O exemplo a seguir executa uma tarefa de preparação distribuído usando o método de servidor de parâmetro com quatro funções de trabalho distribuídas entre dois nós.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Tenha em atenção no `TF_CONFIG`

Também precisará os endereços de rede e portas do cluster para o [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), por isso, o Azure Machine Learning define o `TF_CONFIG` variável de ambiente para.

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

Para o nível mais alto do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analisar isso `TF_CONFIG` variável e o cluster de compilação especificar para.

Para nível inferior APIs centrais do TensorFlow para treinamento, analisar o `TF_CONFIG` variável e compilação o `tf.train.ClusterSpec` em seu código de treinamento. Na [neste exemplo](https://aka.ms/aml-notebook-tf-ps), faria, neste **seu script de treinamento** da seguinte forma:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Suporte Keras

[Keras](https://keras.io/) é um popular de alto nível DNN Python API que suporta o TensorFlow, CNTK e Theano como back-ends. Se estiver a utilizar o TensorFlow como back-end, a adição de Keras é tão simples quanto incluindo um `pip_package` parâmetro de construtor.

O exemplo seguinte cria um [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator e submete-a como uma experimentação. O avaliador de executa o script de treinamento do Keras `keras_train.py`. A tarefa será executada num ativadas para gpu [destino de computação](how-to-set-up-training-targets.md) com Keras instalado como uma dependência através do pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exportar para ONNX

Para obter inferência otimizada com o [ONNX Runtime](concept-onnx.md), pode converter o modelo treinado do TensorFlow em formato ONNX. Consulte a [exemplo](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Exemplos

Pode encontrar exemplos de código de trabalho para o nó único e distribuídas execuções de TensorFlow com diversas estruturas no [nossa página do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Passos Seguintes

* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)