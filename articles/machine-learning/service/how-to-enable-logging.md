---
title: Ative o registo no serviço Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como ativar o registo no serviço Azure Machine Learning, utilizando os dois predefinido de pacote de registo do Python, bem como utilizar a funcionalidade específica do SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: 8e39734a6f5fa13240d24eb35c431f746e540484
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342127"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Ative o registo no serviço Azure Machine Learning

O SDK de Python do Azure Machine Learning permite-lhe ativar o registo através do pacote de registo de Python padrão, bem como utilizar a funcionalidade de SDK específica para o registo local e o registo da sua área de trabalho no portal do. Registos de fornecem aos desenvolvedores as informações em tempo real sobre o estado da aplicação e podem ajudar a diagnosticar erros ou avisos. Neste artigo, vai aprender diferentes formas de ativar o registo nas seguintes áreas:

> [!div class="checklist"]
> * Modelos de formação e destinos de computação
> * Criação de imagens
> * Modelos implementados
> * Python `logging` definições

Utilize o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar o SDK, e [começar](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python) com o SDK para criar uma área de trabalho no Portal do Azure.

## <a name="training-models-and-compute-target-logging"></a>Modelos de treinamento e o registo de destino de computação

Existem várias formas de ativar o registo durante o processo de treinamento de modelo e os exemplos mostrados ilustrará padrões comuns de design. Pode registar facilmente dados relacionados com a execução para a área de trabalho na cloud utilizando o `start_logging` funcionar no `Experiment` classe.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Consulte a documentação de referência para o [executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) classe para as funções de registo adicional.

Para ativar o registo local do Estado da aplicação durante o curso de treinamento, utilize o `show_output` parâmetro. Ativar registo verboso permite-lhe ver os detalhes do processo de treinamento, bem como informações sobre todos os recursos remotos ou destinos de computação. Utilize o seguinte código para ativar o registo após a submissão de experimentação.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Também pode utilizar o mesmo parâmetro no `wait_for_completion` função na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

O SDK também suporta a utilização do pacote de registo de python padrão em determinados cenários para treinamento. O exemplo seguinte ativa um nível de registo do `INFO` num `AutoMLConfig` objeto.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Também pode utilizar o `show_output` parâmetro ao criar um destino de computação persistente. Especificar o parâmetro no `wait_for_completion` função para ativar o registo durante a criação de destino de computação.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Registro em log durante a criação de imagens

Ativar o registo durante a criação de imagens permitirá que consulte os erros durante o processo de compilação. Definir o `show_output` param no `wait_for_deployment()` função.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Registo para modelos implementados

Para obter os registos de um serviço web anteriormente implementada, o serviço de carregar e usar o `get_logs()` função. Os registos podem conter informações detalhadas sobre quaisquer erros que ocorreram durante a implementação.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Também pode iniciar personalizado de pilha rastreios do web Service ao ativar o Application Insights, que lhe permite tempos de pedido/resposta do monitor, taxas de falhas e exceções. Chamar o `update()` função num serviço web existente para ativar o Application Insights.

```python
service.update(enable_app_insights=True)
```

Consulte a [procedimentos](how-to-enable-app-insights.md#enable-and-disable-in-the-portal) para obter mais informações sobre como trabalhar com o Application Insights no portal do Azure.

## <a name="python-native-logging-settings"></a>Definições de registo nativo do Python

Determinados registos no SDK podem conter um erro que indica ao definir o nível de registo para DEBUG. Para definir o nível de registo, adicione o seguinte código ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```