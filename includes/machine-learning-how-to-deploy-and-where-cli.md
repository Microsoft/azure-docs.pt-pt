---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542810"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](../articles/machine-learning/how-to-manage-workspace.md)
- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).
- [A extensão da Interface da Linha de Comando Azure (CLI) para o serviço de machine learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

Siga as instruções da documentação do Azure CLI para [definir o seu contexto de subscrição](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Então faça:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

para ver os espaços de trabalho a que tem acesso.

## <a name="register-your-model"></a><a id="registermodel"></a>Registe o seu modelo

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP]
> Ao registar um modelo, fornece-se o caminho de uma localização em nuvem (de uma corrida de treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no roteiro de entrada. Para obter mais informações, consulte [localizar ficheiros de modelos no seu script de entrada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Os modelos de machine learning estão registados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registar um modelo, pode opcionalmente fornecer metadados sobre o modelo. Os `tags` `properties` dicionários que se aplica a um registo de modelo podem então ser usados para filtrar modelos.

Os exemplos que se seguem demonstram como registar um modelo.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registar um modelo de um treino Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

O `--asset-path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `--asset-path` para o caminho de uma pasta que contém os ficheiros.

### <a name="register-a-model-from-a-local-file"></a>Registar um modelo a partir de um arquivo local

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Para incluir vários ficheiros no registo do modelo, definido `-p` para o caminho de uma pasta que contém os ficheiros.

Para obter mais `az ml model register` informações, consulte a [documentação de referência.](/cli/azure/ext/azure-cli-ml/ml/model)

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

O seguinte comando demonstra como implantar um modelo utilizando o CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes definições:

* Que o modelo requer Python
* O [script de entrada](#define-an-entry-script), que é usado para lidar com pedidos web enviados para o serviço implantado
* O ficheiro Conda que descreve os pacotes Python necessários para a inferência

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Escolha um alvo de cálculo

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implementação

As opções disponíveis para uma configuração de implementação diferem consoante o alvo de computação que escolher.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte o [modelo az ml a implementar](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) documentação.

## <a name="deploy-your-model"></a>Implemente o seu modelo

Está agora pronto para lançar o seu modelo.

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
## <a name="delete-resources"></a>Eliminar recursos

Para eliminar um serviço web implantado, utilize `az ml service <name of webservice>` .

Para eliminar um modelo registado do seu espaço de trabalho, utilize`az ml model delete <model id>`

Leia mais sobre [a eliminação de um webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e [a eliminação de um modelo](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)