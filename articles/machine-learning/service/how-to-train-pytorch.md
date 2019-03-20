---
title: Utilizar modelos com PyTorch
titleSuffix: Azure Machine Learning service
description: Saiba como executar o nó único e distribuída formação de modelos de PyTorch com o avaliador de PyTorch
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a5ddc17f6200ba2d43d67fcd2e4bcc35c224e6cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004067"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Utilizar modelos de PyTorch com o serviço Azure Machine Learning

Para aprender de rede neurais profundas (DNN) com o PyTorch, o Azure Machine Learning fornece um personalizado [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) classe do `Estimator`. O Azure SDK `PyTorch` estimator permite-lhe submeter facilmente trabalhos de treinamento de PyTorch para execuções de nó único e distribuídos na computação do Azure.

## <a name="single-node-training"></a>Treinamento de nó único
Treinamento com o `PyTorch` estimator é semelhante à utilização do [base `Estimator` ](how-to-train-ml-models.md), leia o artigo que mostra como primeiro e certifique-se de que compreende os conceitos apresentados aqui.
  
Para executar uma tarefa de PyTorch, instanciar um `PyTorch` objeto. Deverá ter já criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target` e a sua [arquivo de dados](how-to-access-data.md) objeto `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Aqui, podemos especificar os parâmetros seguintes para o construtor de PyTorch:

Parâmetro | Descrição
--|--
`source_directory` |  Diretório de local que contém todos os seus códigos necessários para a tarefa de preparação. Esta pasta é copiada a partir do seu computador local para a computação remota
`script_params` |  Especificar os argumentos da linha de comandos para o script de treinamento de dicionário `entry_script`, na forma de < argumento da linha de comandos, valor > pares
`compute_target` |  Destino de computação remota que o script de treinamento serão executados no, neste caso uma computação do Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) cluster
`entry_script` |  Caminho do ficheiro (relativa a `source_directory`) do script de treinamento para ser executado na computação remota. Este ficheiro e todos os arquivos adicionais depende, devem ser localizados na pasta
`conda_packages` |  Lista de pacotes de Python a serem instalados por meio de conda necessário ao seu script de treinamento. O construtor tem outro parâmetro chamado `pip_packages` que pode utilizar quaisquer pacotes de pip necessário
`use_gpu` |  Definir este sinalizador `True` para tirar partido de GPU para treinamento. Assume a predefinição `False`

Como está usando o `PyTorch` estimator, o contentor usado para treinamento irão incluir o pacote de PyTorch e dependências relacionadas necessárias para treinamento em CPUs e GPUs.

Em seguida, submeta a tarefa de PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Formação distribuída
O `PyTorch` estimator também permite-lhe treinar seus modelos à escala em clusters GPU e CPU de VMs do Azure. Pode facilmente executar distribuído PyTorch treinamento com algumas chamadas de API, enquanto o Azure Machine Learning gerenciará em segundo plano, a infraestrutura e o necessário para executar estas cargas de trabalho de orquestração.

O Azure Machine Learning suporta, atualmente, com base em MPI formação distribuída de PyTorch usando a estrutura de Horovod.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) é uma arquitetura de código-fonte aberto em anel-allreduce para treinamento distribuído desenvolvidos por Uber.

Para executar PyTorch distribuído usando a estrutura de Horovod, crie o objeto de PyTorch da seguinte forma:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Este código expõe os seguintes novos parâmetros para o construtor de PyTorch:

Parâmetro | Descrição | Predefinição
--|--|--
`node_count` |  Número de nós a utilizar para a tarefa de preparação. | `1`
`process_count_per_node` |  Número de processos (ou "funcionários") para executar em cada nó. | `1`
`distributed_backend` |  Back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI.  Para a realização do treinamento paralelo ou distribuído (por exemplo, `node_count`> 1 ou `process_count_per_node`> 1 ou ambos) com MPI (e Horovod), defina `distributed_backend='mpi'`. A implementação de MPI utilizada pelo Azure Machine Learning é [MPI aberto](https://www.open-mpi.org/). | `None`

O exemplo acima executará formação distribuída com duas funções de trabalho, um operador por nó.

Horovod e as respetivas dependências serão instaladas para, para que pode importá-lo no seu script de treinamento simplesmente `train.py` da seguinte forma:
```Python
import torch
import horovod
```

Por fim, submeta a tarefa de PyTorch distribuída:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Exemplos

Para blocos de notas na aprendizagem profunda distribuída, consulte:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
