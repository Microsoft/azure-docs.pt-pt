---
title: Ativar a exploração madeireira em Azure Machine Learning
description: Saiba como ativar o registo em Azure Machine Learning utilizando tanto o pacote de registo padrão python, como utilizando a funcionalidade específica do SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78396148"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Ativar a exploração madeireira em Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O Azure Machine Learning Python SDK permite-lhe ativar a exploração de madeira utilizando tanto o pacote de registo padrão python, como utilizar a funcionalidade específica do SDK tanto para a exploração madeireira local como para o registo no seu espaço de trabalho no portal. Os registos fornecem aos desenvolvedores informações em tempo real sobre o estado da aplicação, e podem ajudar no diagnóstico de erros ou avisos. Neste artigo, aprende-se diferentes formas de permitir a exploração madeireira nas seguintes áreas:

> [!div class="checklist"]
> * Modelos de formação e metas de computação
> * Criação de imagem
> * Modelos implantados
> * Definições de Python `logging`

[Crie um espaço de trabalho azure machine learning.](how-to-manage-workspace.md) Utilize o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para mais informações sobre o SDK.

## <a name="training-models-and-compute-target-logging"></a>Modelos de formação e registo de alvos de computação

Existem várias formas de permitir a exploração madeireira durante o processo de treino do modelo, e os exemplos mostrados ilustram padrões de design comuns. Pode aceder facilmente a dados relacionados com o `start_logging` funcionamento `Experiment` do seu espaço de trabalho na nuvem utilizando a função na classe.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Consulte a documentação de referência para a classe [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) para obter funções adicionais de exploração madeireira.

Para permitir o registo local do estado `show_output` de aplicação durante o progresso da formação, utilize o parâmetro. Permitir a exploração madeireira verbosa permite-lhe ver detalhes do processo de treino, bem como informações sobre quaisquer recursos remotos ou alvos de computação. Utilize o seguinte código para permitir o registo de registo após a submissão da experiência.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Também pode utilizar o mesmo `wait_for_completion` parâmetro na função na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

O SDK também suporta a utilização do pacote de registo de python padrão em certos cenários para treino. O exemplo seguinte permite um `INFO` nível de registo de um `AutoMLConfig` objeto.

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

Também pode utilizar `show_output` o parâmetro ao criar um alvo de cálculo persistente. Especifique o `wait_for_completion` parâmetro na função para permitir a exploração do registo durante a criação do alvo do cálculo.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Exploração madeireira para modelos implantados

Para recuperar registos de um serviço web previamente implantado, carregue o serviço e utilize a `get_logs()` função. Os registos podem conter informações detalhadas sobre quaisquer erros ocorridos durante a implementação.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Também pode registar vestígios de pilhas personalizados para o seu serviço web, permitindo insights de aplicação, o que lhe permite monitorizar os tempos de pedido/resposta, taxas de falha e exceções. Ligue `update()` para a função num serviço web existente para ativar os Insights de Aplicação.

```python
service.update(enable_app_insights=True)
```

Para mais informações, consulte [o Monitor e recolha dados de pontos finais do serviço web ML](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Definições de exploração madeireira nativa python

Certos registos no SDK podem conter um erro que o instrui a definir o nível de registo para DEBUG. Para definir o nível de registo, adicione o seguinte código ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar e recolher dados de pontos finais do serviço web ml](how-to-enable-app-insights.md)