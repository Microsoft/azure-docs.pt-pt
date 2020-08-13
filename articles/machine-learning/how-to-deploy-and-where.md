---
title: Como e onde implementar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implementar os seus modelos de Machine Learning Azure, incluindo instâncias de contentores Azure, Serviço Azure Kubernetes, Azure IoT Edge e conjuntos de portas programáveis em campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e8aa87eecc29a5b664520cb1be2cbc5e65b2969e
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134552"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implementar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar o seu modelo de machine learning como um serviço web na nuvem Azure ou para dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante, independentemente de onde implementa o modelo:

1. Registar o modelo.
1. Preparar uma configuração de inferência
1. Preparar um script de entrada (a menos que utilize [a implementação sem código)](how-to-deploy-no-code-deployment.md)
1. Implementar o modelo para o destino de computação.
1. Teste o modelo implantado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerir, implementar e monitorizar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).
- [A extensão da Interface da Linha de Comando Azure (CLI) para o serviço de machine learning](reference-azure-machine-learning-cli.md)

# <a name="python"></a>[Python](#tab/python)

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).
- O [kit de desenvolvimento de software Azure Machine Learning (SDK) para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

---

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Siga as instruções da documentação do Azure CLI para [definir o seu contexto de subscrição](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Então faça:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

para ver os espaços de trabalho a que tem acesso.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Para obter mais informações sobre a utilização do SDK para ligar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) documentação Python.


---


## <a name="register-your-model"></a><a id="registermodel"></a>Registe o seu modelo

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP]
> Ao registar um modelo, fornece-se o caminho de uma localização em nuvem (de uma corrida de treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no roteiro de entrada. Para obter mais informações, consulte [localizar ficheiros de modelos no seu script de entrada](how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de machine learning estão registados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registar um modelo, pode opcionalmente fornecer metadados sobre o modelo. Os `tags` `properties` dicionários que se aplica a um registo de modelo podem então ser usados para filtrar modelos.

Os exemplos que se seguem demonstram como registar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registar um modelo de um treino Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

O `--asset-path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `--asset-path` para o caminho de uma pasta que contém os ficheiros.

### <a name="register-a-model-from-a-local-file"></a>Registar um modelo a partir de um arquivo local

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Para incluir vários ficheiros no registo do modelo, definido `-p` para o caminho de uma pasta que contém os ficheiros.

Para obter mais `az ml model register` informações, consulte a [documentação de referência.](/cli/azure/ext/azure-cli-ml/ml/model)

# <a name="python"></a>[Python](#tab/python)


Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP]
> Ao registar um modelo, fornece-se o caminho de uma localização em nuvem (de uma corrida de treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no roteiro de entrada. Para obter mais informações, consulte [localizar ficheiros de modelos no seu script de entrada](how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de machine learning estão registados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registar um modelo, pode opcionalmente fornecer metadados sobre o modelo. Os `tags` `properties` dicionários que se aplica a um registo de modelo podem então ser usados para filtrar modelos.

Os exemplos que se seguem demonstram como registar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registar um modelo de um treino Azure ML

  Quando utilizar o SDK para treinar um modelo, pode receber um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo da forma como treinou o modelo. Cada objeto pode ser usado para registar um modelo criado por uma experiência.

  + Registar um modelo a partir de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `model_path` para o caminho de uma pasta que contém os ficheiros. Para mais informações, consulte a documentação [run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registar um modelo a partir de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` parâmetros não são especificados, pelo que a iteração com a melhor métrica primária será registada. O `model_id` valor devolvido da corrida é usado em vez de um nome de modelo.

    Para mais informações, consulte a documentação [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)


### <a name="register-a-model-from-a-local-file"></a>Registar um modelo a partir de um arquivo local

Pode registar um modelo fornecendo o percurso local do modelo. Pode fornecer o caminho de uma pasta ou de um único ficheiro. Pode utilizar este método para registar modelos treinados com Azure Machine Learning e depois descarregados. Também pode utilizar este método para registar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Utilizando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir vários ficheiros no registo do modelo, definido `model_path` para o caminho de uma pasta que contém os ficheiros.

Para mais informações, consulte a documentação para a [classe Modelo.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)

Para obter mais informações sobre o trabalho com modelos treinados fora do Azure Machine Learning, consulte [Como implementar um modelo existente.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O seguinte comando demonstra como implantar um modelo utilizando o CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes definições:

* Que o modelo requer Python
* O [script de entrada](#define-an-entry-script), que é usado para lidar com pedidos web enviados para o serviço implantado
* O ficheiro Conda que descreve os pacotes Python necessários para a inferência

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

Uma configuração de inferência descreve como configurar o serviço web que contém o seu modelo. É usado mais tarde, quando se implanta o modelo.

A configuração de inferência utiliza ambientes de aprendizagem automática Azure para definir as dependências de software necessárias para a sua implementação. Os ambientes permitem criar, gerir e reutilizar as dependências de software necessárias para o treino e implantação. Pode criar um ambiente a partir de ficheiros de dependência personalizados ou usar um dos ambientes de aprendizagem automática Azure com curadoria. O YAML seguinte é um exemplo de um ficheiro de dependências Conda para inferência. Note que deve indicar padrão de azureml com verion >= 1.0.45 como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. Se quiser utilizar a geração automática de esquemas, o seu script de entrada também deve importar os `inference-schema` pacotes.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se a sua dependência estiver disponível tanto através da Conda como do pip (do PyPi), a Microsoft recomenda a utilização da versão Conda, uma vez que os pacotes Conda normalmente vêm com binários pré-construídos que tornam a instalação mais fiável.
>
> Para mais informações, consulte [Understanding Conda e Pip.](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Para verificar se a sua dependência está disponível através da Conda, utilize o `conda search <package-name>` comando ou utilize os índices de pacotes em e [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Pode utilizar o ficheiro dependências para criar um objeto ambiental e guardá-lo no seu espaço de trabalho para utilização futura:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Para uma discussão aprofundada sobre a utilização e personalização de ambientes Python com Azure Machine Learning, consulte [Criar & utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md)

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).


O exemplo a seguir demonstra o carregamento de um ambiente a partir do seu espaço de trabalho e, em seguida, a sua utilização com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

---

## <a name="choose-a-compute-target"></a>Escolha um alvo de cálculo

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implementação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

As opções disponíveis para uma configuração de implementação diferem consoante o alvo de computação que escolher.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte o [modelo az ml a implementar](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) documentação.

# <a name="python"></a>[Python](#tab/python)

Antes de implementar o seu modelo, tem de definir a configuração de implementação. *A configuração de implementação é específica do alvo do cálculo que irá hospedar o serviço web.* Por exemplo, quando implementa um modelo localmente, deve especificar a porta onde o serviço aceita pedidos. A configuração de implementação não faz parte do seu script de entrada. É usado para definir as características do alvo do cálculo que irá hospedar o modelo e o script de entrada.

Também poderá ter de criar o recurso compute, se, por exemplo, ainda não tiver uma instância do Serviço Azure Kubernetes (AKS) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implementação para cada alvo de cálculo:

| Destino de computação | Exemplo de configuração de implementação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para instâncias locais, azure container e serviços web AKS podem ser importadas a partir `azureml.core.webservice` de:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---



## <a name="deploy-your-model"></a>Implemente o seu modelo

Está agora pronto para lançar o seu modelo. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Usando um modelo registado

Se registou o seu modelo no seu espaço de trabalho Azure Machine Learning, substitua o "mymodel:1" pelo nome do seu modelo e pelo seu número de versão.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Usando um modelo local

Se preferir não registar o seu modelo, pode passar o parâmetro "sourceDirectory" no seu inferenceconfig.jspara especificar um diretório local a partir do qual servir o seu modelo.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

O exemplo abaixo demonstra uma implantação local. A sintaxe variará dependendo do alvo de computação que escolheu no passo anterior.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para mais informações, consulte a documentação para [LocalWebservice,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py) [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

---

### <a name="understanding-service-state"></a>Estado de serviço de compreensão

Durante a implementação do modelo, poderá ver a alteração do estado de serviço enquanto este se desdobra completamente.

A tabela a seguir descreve os diferentes estados de serviço:

| Estado do serviço web | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | Não |
| Mau estado de funcionamento | O serviço foi implantado, mas está atualmente inacessível.  | Não |
| Insodulável | O serviço não pode ser implantado neste momento devido à falta de recursos. | Não |
| Com falhas | O serviço falhou em ser acionado devido a um erro ou acidente. | Sim |
| Bom estado de funcionamento | O serviço é saudável e o ponto final está disponível. | Sim |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência do lote
Os alvos Azure Machine Learning Compute são criados e geridos pela Azure Machine Learning. Podem ser utilizados para a previsão de lote a partir de oleodutos Azure Machine Learning.

Para uma passagem de inferência de lote com Azure Machine Learning Compute, consulte [Como executar previsões de lotes](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência IoT Edge
O suporte para implantação na borda está em pré-visualização. Para obter mais informações, consulte [implementar a Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Eliminar recursos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para eliminar um serviço web implantado, utilize `az ml service <name of webservice>` .

Para eliminar um modelo registado do seu espaço de trabalho, utilize`az ml model delete <model id>`

Leia mais sobre [a eliminação de um webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e [a eliminação de um modelo](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)

# <a name="python"></a>[Python](#tab/python)

Para eliminar um serviço web implantado, utilize `service.delete()` .
Para eliminar um modelo registado, utilize `model.delete()` .

Para obter mais informações, consulte a documentação para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

---


## <a name="next-steps"></a>Próximos passos

* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)

