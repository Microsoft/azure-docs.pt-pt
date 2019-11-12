---
title: Habilitar logon Azure Machine Learning
description: Saiba como habilitar o registro em log no Azure Machine Learning usando o pacote de log do Python padrão, bem como usando a funcionalidade específica do SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 91e00b8e1d13f69ae3ba446bcbc09f06f387c439
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931122"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Habilitar logon Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

O SDK do Python Azure Machine Learning permite habilitar o registro em log usando o pacote de log do Python padrão, bem como usar a funcionalidade específica do SDK para registro em log local e registro em log em seu espaço de trabalho no Portal. Os logs fornecem aos desenvolvedores informações em tempo real sobre o estado do aplicativo e podem ajudar no diagnóstico de erros ou avisos. Neste artigo, você aprende diferentes maneiras de habilitar o registro em log nas seguintes áreas:

> [!div class="checklist"]
> * Modelos de treinamento e destinos de computação
> * Criação de imagem
> * Modelos implantados
> * Configurações de `logging` do Python

[Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md). Use o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre o SDK.

## <a name="training-models-and-compute-target-logging"></a>Modelos de treinamento e log de destino de computação

Há várias maneiras de habilitar o registro em log durante o processo de treinamento do modelo, e os exemplos mostrados ilustrarão os padrões de design comuns. Você pode facilmente registrar em log dados relacionados à execução em seu espaço de trabalho na nuvem usando a função `start_logging` na classe `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Consulte a documentação de referência para a classe [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) para funções de log adicionais.

Para habilitar o log local do estado do aplicativo durante o andamento do treinamento, use o parâmetro `show_output`. Habilitar o log detalhado permite que você veja detalhes do processo de treinamento, bem como informações sobre quaisquer destinos de computação ou recursos remotos. Use o código a seguir para habilitar o registro em log após o envio do teste.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Você também pode usar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

O SDK também dá suporte ao uso do pacote de log do Python padrão em determinados cenários para treinamento. O exemplo a seguir habilita um nível de log de `INFO` em um objeto `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Você também pode usar o parâmetro `show_output` ao criar um destino de computação persistente. Especifique o parâmetro na função `wait_for_completion` para habilitar o registro em log durante a criação do destino de computação.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Registro em log durante a criação da imagem

Habilitar o log durante a criação da imagem permitirá que você veja os erros durante o processo de compilação. Defina o parâmetro `show_output` na função `wait_for_deployment()`.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Registro em log para modelos implantados

Para recuperar os logs de um serviço Web implantado anteriormente, carregue o serviço e use a função `get_logs()`. Os logs podem conter informações detalhadas sobre os erros ocorridos durante a implantação.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Você também pode registrar rastreamentos de pilha personalizados para o serviço Web habilitando Application Insights, que permite monitorar tempos de solicitação/resposta, taxas de falha e exceções. Chame a função `update()` em um serviço Web existente para habilitar Application Insights.

```python
service.update(enable_app_insights=True)
```

Consulte o " [como](how-to-enable-app-insights.md) " para obter mais informações sobre como trabalhar com Application Insights no Azure Machine Learning Studio.

## <a name="python-native-logging-settings"></a>Configurações de log nativo do Python

Determinados logs no SDK podem conter um erro que instrui você a definir o nível de log para depurar. Para definir o nível de log, adicione o código a seguir ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
