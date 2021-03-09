---
title: Como implementar modelos de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Saiba como e onde implementar modelos de aprendizagem automática. Implementar para Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge e FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: fa68db4bd166ebe1acd1ae85fca2d7e51236a4c4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522058"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Implementar modelos de aprendizagem automática para Azure

Aprenda a implementar o seu modelo de aprendizagem automática ou aprendizagem profunda como um serviço web na nuvem Azure. Também pode ser implantado em dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante, independentemente de onde implementa o modelo:

1. Registe o modelo (opcional, ver abaixo).
1. Prepare uma configuração de inferência (a menos que utilize [uma implementação sem código).](./how-to-deploy-no-code-deployment.md)
1. Prepare um script de entrada (a menos que utilize [uma implementação sem código).](./how-to-deploy-no-code-deployment.md)
1. Escolha um alvo de cálculo.
1. Implementar o modelo para o destino de computação.
1. Teste o serviço web resultante.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implementação de machine learning, consulte [Gerir, implementar e monitorizar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).
- [A extensão da Interface da Linha de Comando Azure (CLI) para o serviço machine learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).
- O [kit de desenvolvimento de software Azure Machine Learning (SDK) para Python](/python/api/overview/azure/ml/intro).

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

Para obter mais informações sobre a utilização do SDK para ligar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](/python/api/overview/azure/ml/intro#workspace) documentação Python.


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registe o seu modelo (opcional)

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP] 
> Recomenda-se o registo de um modelo de rastreio de versão, mas não é necessário. Se preferir prosseguir sem registar um modelo, terá de especificar um diretório de origem no seu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) ou [inferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) e garantir que o seu modelo reside dentro desse diretório de origem.

> [!TIP]
> Ao registar um modelo, fornece-se o caminho de uma localização em nuvem (de uma corrida de treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no roteiro de entrada. Para obter mais informações, consulte [localizar ficheiros de modelos no seu script de entrada](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Ao utilizar o Filtro por `Tags` opção na página de Modelos do Azure Machine Learning Studio, em vez de utilizar `TagName : TagValue` os clientes deve usar `TagName=TagValue` (sem espaço)

Os exemplos que se seguem demonstram como registar um modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

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

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registar um modelo de um treino Azure ML

  Quando utilizar o SDK para treinar um modelo, pode receber um objeto [Run](/python/api/azureml-core/azureml.core.run.run) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo da forma como treinou o modelo. Cada objeto pode ser usado para registar um modelo criado por uma experiência.

  + Registar um modelo a partir de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `model_path` para o caminho de uma pasta que contém os ficheiros. Para mais informações, consulte a documentação [Run.register_model.](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

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

Para mais informações, consulte a documentação para a [classe Modelo.](/python/api/azureml-core/azureml.core.model.model)

Para obter mais informações sobre o trabalho com modelos treinados fora do Azure Machine Learning, consulte [Como implementar um modelo existente.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência


Uma configuração de inferência descreve como configurar o serviço web que contém o seu modelo. É usado mais tarde, quando se implanta o modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Uma configuração mínima de inferência pode ser escrita como:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Isto especifica que a implementação de machine learning utilizará o ficheiro `score.py` no `./working_dir` diretório para processar pedidos de entrada.

[Consulte este artigo](./reference-azure-machine-learning-cli.md#inference-configuration-schema) para uma discussão mais aprofundada sobre as configurações de inferência. 

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra:

1. carregando um [ambiente curado](resource-curated-environments.md) do seu espaço de trabalho
1. Clonagem do ambiente
1. Especificando `scikit-learn` como uma dependência.
1. Usar o ambiente para criar um InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

---

> [!TIP] 
> Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Escolha um alvo de cálculo

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implementação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

As opções disponíveis para uma configuração de implementação diferem consoante o alvo de computação que escolher.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte [esta referência.](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)

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

## <a name="deploy-your-machine-learning-model"></a>Implemente o seu modelo de aprendizagem automática

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

Para mais informações, consulte a documentação para [LocalWebservice,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice) [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Estado de serviço de compreensão

Durante a implementação do modelo, poderá ver a alteração do estado de serviço enquanto este se desdobra completamente.

A tabela a seguir descreve os diferentes estados de serviço:

| Estado do serviço web | Description | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | No |
| Mau estado de funcionamento | O serviço foi implantado, mas está atualmente inacessível.  | No |
| Insodulável | O serviço não pode ser implantado neste momento devido à falta de recursos. | No |
| Com falhas | O serviço falhou em ser acionado devido a um erro ou acidente. | Yes |
| Bom estado de funcionamento | O serviço é saudável e o ponto final está disponível. | Yes |

> [!TIP]
> Ao implementar, as imagens do Docker para alvos de computação são construídas e carregadas a partir do Registo de Contentores de Azure (ACR). Por predefinição, o Azure Machine Learning cria um ACR que utiliza o nível *básico* de serviço. Mudar o ACR para o seu espaço de trabalho para um nível normal ou premium pode reduzir o tempo que leva para construir e implementar imagens para os seus alvos de computação. Para mais informações, consulte [os níveis de serviço de registo de contentores Azure.](../container-registry/container-registry-skus.md)

> [!NOTE]
> Se estiver a implementar um modelo para o Serviço Azure Kubernetes (AKS), aconselhamos que adinga [o Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) para esse cluster. Isto irá ajudá-lo a compreender a saúde geral do cluster e o uso de recursos. Também poderá encontrar os seguintes recursos úteis:
>
> * [Verifique se os eventos de Saúde de Recursos impactam o seu cluster AKS](../aks/aks-resource-health.md)
> * [Diagnóstico de Serviço Azure Kubernetes](../aks/concepts-diagnostics.md)
>
> Se estiver a tentar implantar um modelo num cluster pouco saudável ou sobrecarregado, espera-se que experimente problemas. Se precisar de ajuda para resolver problemas de resolução de problemas de cluster AKS contacte o Suporte AKS.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Inferência do lote
Os alvos Azure Machine Learning Compute são criados e geridos pela Azure Machine Learning. Podem ser utilizados para a previsão de lote a partir de oleodutos Azure Machine Learning.

Para uma passagem de inferência de lote com Azure Machine Learning Compute, consulte [Como executar previsões de lotes](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> Inferência IoT Edge
O suporte para implantação na borda está em pré-visualização. Para obter mais informações, consulte [implementar a Azure Machine Learning como um módulo IoT Edge](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Eliminar recursos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para eliminar um serviço web implantado, utilize `az ml service <name of webservice>` .

Para eliminar um modelo registado do seu espaço de trabalho, utilize `az ml model delete <model id>`

Leia mais sobre [a eliminação de um serviço web](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e a [eliminação de um modelo.](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)

# <a name="python"></a>[Python](#tab/python)

Para eliminar um serviço web implantado, utilize `service.delete()` .
Para eliminar um modelo registado, utilize `model.delete()` .

Para obter mais informações, consulte a documentação para [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) e [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de uma implementação com falhas](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)