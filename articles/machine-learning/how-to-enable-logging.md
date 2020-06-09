---
title: Ativar o registo em Azure Machine Learning
description: Saiba como permitir o início de sessão no Azure Machine Learning utilizando o pacote de registo python predefinido, bem como utilizando a funcionalidade específica SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: trbye
ms.date: 03/05/2020
ms.custom: tracking-python
ms.openlocfilehash: 25c0f906cdf8a351d868dcae0b794d4ea833466e
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560244"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Ativar o registo em Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O Azure Machine Learning Python SDK permite-lhe permitir iniciar sessão usando o pacote de registo padrão python, bem como usar a funcionalidade específica SDK tanto para o registo local como para o login no seu espaço de trabalho no portal. Os registos fornecem aos desenvolvedores informações em tempo real sobre o estado da aplicação, e podem ajudar no diagnóstico de erros ou avisos. Neste artigo, aprende-se diferentes formas de permitir o registo de madeira nas seguintes áreas:

> [!div class="checklist"]
> * Modelos de formação e metas de computação
> * Criação de imagem
> * Modelos implantados
> * `logging`Configurações python

Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md) Utilize o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre o SDK.

## <a name="training-models-and-compute-target-logging"></a>Modelos de formação e registo de alvos de computação

Existem várias formas de permitir a exploração madeireira durante o processo de treino do modelo, e os exemplos mostrados ilustrarão padrões de design comuns. Pode registar facilmente dados relacionados com a execução do seu espaço de trabalho na nuvem utilizando a `start_logging` função na `Experiment` classe.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Consulte a documentação de referência para a classe [Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) para obter funções adicionais de registo.

Para permitir o registo local do estado de aplicação durante o progresso da formação, utilize o `show_output` parâmetro. Ativar a registo verboso permite-lhe ver detalhes do processo de treino, bem como informações sobre quaisquer recursos remotos ou metas de computação. Utilize o seguinte código para permitir o registo após a submissão de experiências.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Também pode utilizar o mesmo parâmetro na `wait_for_completion` função na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

O SDK também suporta a utilização do pacote de registo de python predefinido em certos cenários para o treino. O exemplo a seguir permite um nível de registo de `INFO` um `AutoMLConfig` objeto.

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

Também pode utilizar o `show_output` parâmetro ao criar um alvo de computação persistente. Especifique o parâmetro na `wait_for_completion` função para permitir o registo durante a criação do alvo do cálculo.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Registo de modelos implantados

Para obter registos de um serviço web previamente implantado, carregue o serviço e utilize a `get_logs()` função. Os registos podem conter informações detalhadas sobre quaisquer erros ocorridos durante a implantação.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Também pode registar rastreios de pilhas personalizados para o seu serviço web, ativando o Application Insights, que lhe permite monitorizar os tempos de pedido/resposta, taxas de falha e exceções. Ligue para a `update()` função num serviço web existente para ativar o Application Insights.

```python
service.update(enable_app_insights=True)
```

Para obter mais informações, consulte [o Monitor e recolha dados dos pontos finais do serviço web ML](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Definições de registo nativo python

Certos registos no SDK podem conter um erro que o instrui a definir o nível de registo para DEBUG. Para definir o nível de registo, adicione o seguinte código ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Passos seguintes

* [Monitorize e recolha dados dos pontos finais do serviço web ML](how-to-enable-app-insights.md)
