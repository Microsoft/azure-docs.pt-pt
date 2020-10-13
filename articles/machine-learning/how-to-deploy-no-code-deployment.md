---
title: Sem implantação de código (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar um modelo sem um script de entrada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: 32b2afe036b443846199b5e9d74e690859fb581d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998853"
---
# <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

A implementação de modelos sem código está atualmente em pré-visualização e suporta os seguintes quadros de aprendizagem automática:

## <a name="tensorflow-savedmodel-format"></a>Formato TensorFlow SavedModel
Os modelos TensorFlow precisam de ser registados no **formato SaveModel** para funcionarem sem implementação de modelos sem código.

Consulte [este link](https://www.tensorflow.org/guide/saved_model) para obter informações sobre como criar um SavedModel.

Apoiamos qualquer versão TensorFlow que esteja listada em "Tags" no [TensorFlow Serving DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>Modelos ONNX

O registo e implantação do modelo ONNX é suportado para qualquer gráfico de inferência ONNX. As etapas de pré-processamento e pós-processamento não são suportadas atualmente.

Aqui está um exemplo de como registar e implementar um modelo MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Para marcar um modelo, consulte [Consumir um modelo Azure Machine Learning implementado como um serviço web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service). Muitos projetos onNX usam ficheiros protobuf para armazenar compactamente dados de formação e validação, o que pode dificultar saber qual o formato de dados esperado pelo serviço. Como desenvolvedor de modelos, deve documentar para os seus desenvolvedores:

* Formato de entrada (JSON ou binário)
* Forma e tipo de dados de entrada (por exemplo, uma matriz de flutuadores de forma [100,100,3])
* Informações de domínio (por exemplo, para uma imagem, o espaço de cores, a ordem dos componentes e se os valores estão normalizados)

Se estiver a utilizar o Pytorch, [os modelos de exportação de PyTorch para ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) têm os detalhes sobre a conversão e limitações. 

## <a name="scikit-learn-models"></a>Modelos de aprendizagem de scikit

Nenhuma implementação de modelo de código é suportada para todos os tipos de modelos de aprendizagem de scikit-learn incorporados.

Aqui está um exemplo de como registar e implementar um modelo sklearn sem código extra:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Os modelos que suportam predict_proba utilizarão este método por padrão. Para anular isto para usar, pode modificar o corpo POST como abaixo:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Estas dependências estão incluídas no recipiente de inferência scikit-learn pré-construído:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)
