---
title: Treinar e implantar modelos da CLI
titleSuffix: Azure Machine Learning
description: Saiba como usar a extensão do Machine Learning para CLI do Azure para treinar, registrar e implantar um modelo na linha de comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: f920df20a8dc1cace76f641ce1c71f9b91a30bf4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867678"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: treinar e implantar um modelo da CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa a extensão de aprendizado de máquina para o CLI do Azure para treinar, registrar e implantar um modelo.

Os scripts de treinamento do Python neste tutorial usam o [scikit-Learn](https://scikit-learn.org/) para treinar um modelo básico. O foco deste tutorial não está nos scripts ou no modelo, mas o processo de usar a CLI para trabalhar com Azure Machine Learning.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Instalar a extensão do Machine Learning
> * Criar um espaço de trabalho do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de registros usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar dados usando o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usar o [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="download-the-example-project"></a>Baixar o projeto de exemplo

Para este tutorial, baixe o projeto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . Os arquivos no diretório `examples/cli-train-deploy` são usados pelas etapas neste tutorial.

Para obter uma cópia local dos arquivos, [Baixe um arquivo. zip](https://github.com/microsoft/MLOps/archive/master.zip)ou use o comando git a seguir para clonar o repositório:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Arquivos de treinamento

O diretório `examples/cli-train-deploy` do projeto contém os seguintes arquivos, que são usados ao treinar um modelo:

* `.azureml\mnist.runconfig`: um arquivo de __configuração de execução__ . Esse arquivo define o ambiente de tempo de execução necessário para treinar o modelo. Neste exemplo, ele também monta os dados usados para treinar o modelo no ambiente de treinamento.
* `scripts\train.py`: o script de treinamento. Esse arquivo treina o modelo.
* `scripts\utils.py`: um arquivo auxiliar usado pelo script de treinamento.
* `.azureml\conda_dependencies.yml`: define as dependências de software necessárias para executar o script de treinamento.
* `dataset.json`: a definição de DataSet. Usado para registrar o conjunto de MNIST no espaço de trabalho do Azure Machine Learning.

### <a name="deployment-files"></a>Arquivos de implantação

O repositório contém os seguintes arquivos, que são usados para implantar o modelo treinado como um serviço Web:

* `aciDeploymentConfig.yml`: um arquivo de __configuração de implantação__ . Esse arquivo define o ambiente de hospedagem necessário para o modelo.
* `inferenceConfig.yml`: um arquivo de configuration__ de inferência. Esse arquivo define o ambiente de software usado pelo serviço para pontuar dados com o modelo.
* `score.py`: um script Python que aceita dados de entrada, classifica-os usando o modelo e, em seguida, retorna uma resposta.
* `scoring-env.yml`: as dependências Conda necessárias para executar o modelo e `score.py` script.
* `testdata.json`: um arquivo de dados que pode ser usado para testar o serviço Web implantado.

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais básico é autenticar-se interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

## <a name="install-the-machine-learning-extension"></a>Instalar a extensão do Machine Learning

Para instalar a extensão de Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Se você receber uma mensagem informando que a extensão já está instalada, use o seguinte comando para atualizar para a versão mais recente:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner básico de recursos na plataforma Azure. Ao trabalhar com o Azure Machine Learning, o grupo de recursos conterá seu espaço de trabalho do Azure Machine Learning. Ele também conterá outros serviços do Azure usados pelo espaço de trabalho. Por exemplo, se você treinar seu modelo usando um recurso de computação baseado em nuvem, esse recurso será criado no grupo de recursos.

Para __criar um novo grupo de recursos__, use o comando a seguir. Substitua `<resource-group-name>` pelo nome a ser usado para esse grupo de recursos. Substitua `<location>` pela região do Azure a ser usada para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região onde o Azure Machine Learning está disponível. Para obter informações, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta desse comando é semelhante ao JSON a seguir:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre como trabalhar com grupos de recursos, consulte [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Para criar um novo espaço de trabalho, use o comando a seguir. Substitua `<workspace-name>` pelo nome que você deseja usar para este espaço de trabalho. Substitua `<resource-group-name>` pelo nome do grupo de recursos:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Conectar o projeto local ao espaço de trabalho

Em um terminal ou prompt de comando, use os seguintes comandos para alterar os diretórios para o diretório `cli-train-deploy` e, em seguida, conecte-se ao seu espaço de trabalho:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Este comando cria um arquivo de `.azureml/config.json`, que contém as informações necessárias para se conectar ao seu espaço de trabalho. O restante dos comandos `az ml` usados neste tutorial usará esse arquivo, de modo que você não precisa adicionar o espaço de trabalho e o grupo de recursos a todos os comandos.

## <a name="create-the-compute-target-for-training"></a>Criar o destino de computação para treinamento

Este exemplo usa um cluster de computação Azure Machine Learning para treinar o modelo. Para criar um novo cluster de computação, use o seguinte comando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Este comando cria um novo destino de computação chamado `cpu`, com um máximo de quatro nós. O tamanho da VM selecionado fornece uma VM com um recurso de GPU. Para obter informações sobre o tamanho da VM, consulte [tipos e tamanhos de VM].

> [!IMPORTANT]
> O nome do destino de computação (`cpu` nesse caso), é importante; Ele é referenciado pelo arquivo de `.azureml/mnist.runconfig` usado na próxima seção.

## <a name="define-the-dataset"></a>Definir o conjunto de um

Para treinar um modelo, você pode fornecer os dados de treinamento usando um DataSet. Para criar um conjunto de um DataSet a partir da CLI, você deve fornecer um arquivo de definição de conjunto de um. O arquivo de `dataset.json` fornecido no repositório cria um novo DataSet usando os dados de MNIST. O conjunto de um que ele cria é denominado `mnist-dataset`.

Para registrar o conjunto de registros usando o arquivo de `dataset.json`, use o seguinte comando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```


> [!IMPORTANT]
> Copie o valor da entrada `id`, pois ele é usado na próxima seção.

Para conferir um modelo mais abrangente para o arquivo JSON que descreve um conjunto de informações, use o seguinte comando:
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Referenciar o conjunto de um

Para disponibilizar o conjunto de arquivos no ambiente de treinamento, você deve referenciá-lo do arquivo runconfig. O arquivo de `.azureml/mnist.runconfig` contém as seguintes entradas YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Altere o valor da entrada `id` para corresponder ao valor retornado quando você registrou o conjunto de um. Esse valor é usado para carregar os dados no destino de computação durante o treinamento.

Esse YAML faz o seguinte:

* Monta o DataSet (com base na ID do conjunto de um) no ambiente de treinamento e armazena o caminho para o ponto de montagem na variável de ambiente `mnist`..
* Passa o local dos dados (ponto de montagem) dentro do ambiente de treinamento para o script usando o argumento `--data-folder`.

O arquivo runconfig também contém informações usadas para configurar o ambiente usado pela execução de treinamento. Se você inspecionar esse arquivo, verá que ele faz referência ao destino de computação `cpu-compute` que você criou anteriormente. Ele também lista o número de nós a serem usados ao treinar (`"nodeCount": "4"`) e contém uma seção `"condaDependencies"` que lista os pacotes do python necessários para executar o script de treinamento.

> [!TIP]
> Embora seja possível criar manualmente um arquivo runconfig, aquele neste exemplo foi criado usando o arquivo `generate-runconfig.py` incluído no repositório. Esse arquivo Obtém uma referência ao DataSet registrado, cria uma configuração de execução programaticamente e, em seguida, persiste no arquivo.

Para obter mais informações sobre arquivos de configuração de execução, consulte [configurar e usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)ou fazer referência a esse [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) para ver o esquema completo de um runconfig.

## <a name="submit-the-training-run"></a>Enviar a execução de treinamento

Para iniciar uma execução de treinamento no destino de computação `cpu-compute`, use o seguinte comando:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Este comando especifica um nome para o experimento (`myexperiment`). O experimento armazena informações sobre essa execução no espaço de trabalho.

O parâmetro `-c mnist` especifica o arquivo de `.azureml/mnist.runconfig`.

O parâmetro `-t` armazena uma referência a essa execução em um arquivo JSON e será usado nas próximas etapas para registrar e baixar o modelo.

Como os processos de execução de treinamento, ele transmite informações da sessão de treinamento no recurso de computação remota. Parte das informações é semelhante ao seguinte texto:

```text
Predict the test set
Accuracy is 0.9185
```

Esse texto é registrado no script de treinamento e exibe a precisão do modelo. Outros modelos terão métricas de desempenho diferentes.

Se você inspecionar o script de treinamento, observará que ele também usa o valor alfa ao armazenar o modelo treinado para `outputs/sklearn_mnist_model.pkl`.

O modelo foi salvo no diretório `./outputs` no destino de computação em que foi treinado. Nesse caso, a instância de computação Azure Machine Learning na nuvem do Azure. O processo de treinamento carrega automaticamente o conteúdo do diretório `./outputs` do destino de computação em que o treinamento ocorre em seu espaço de trabalho do Azure Machine Learning. Ele é armazenado como parte do experimento (`myexperiment` neste exemplo).

## <a name="register-the-model"></a>Registe o modelo

Para registrar o modelo diretamente da versão armazenada em seu experimento, use o seguinte comando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Esse comando registra o arquivo de `outputs/sklearn_mnist_model.pkl` criado pela execução de treinamento como um novo registro de modelo chamado `mymodel`. O `--assets-path` faz referência a um caminho em um experimento. Nesse caso, as informações de teste e execução são carregadas do arquivo `runoutput.json` criado pelo comando de treinamento. O `-t registeredmodel.json` cria um arquivo JSON que faz referência ao novo modelo registrado criado por esse comando e é usado por outros comandos da CLI que funcionam com modelos registrados.

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Controle de versão de modelo

Observe o número de versão retornado para o modelo. A versão é incrementada toda vez que você registra um novo modelo com esse nome. Por exemplo, você pode baixar o modelo e registrá-lo de um arquivo local usando os seguintes comandos:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

O primeiro comando baixa o modelo registrado no diretório atual. O nome do arquivo é `sklearn_mnist_model.pkl`, que é o arquivo referenciado quando você registrou o modelo. O segundo comando registra o modelo local (`-p "sklearn_mnist_model.pkl"`) com o mesmo nome do registro anterior (`mymodel`). Desta vez, os dados JSON retornados listam a versão como 2.

## <a name="deploy-the-model"></a>Implementar o modelo

Para implantar um modelo, use o seguinte comando:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Você pode receber um aviso sobre "falha ao verificar a existência de LocalWebservice". Você pode ignorá-lo com segurança, pois não está implantando um serviço Web local.

Este comando implanta um novo serviço chamado `myservice`, usando a versão 1 do modelo que você registrou anteriormente.

O arquivo de `inferenceConfig.yml` fornece informações sobre como executar a inferência, como o script de entrada (`score.py`) e as dependências de software. Para obter mais informações sobre a estrutura desse arquivo, consulte o [esquema de configuração de inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema). Para obter mais informações sobre scripts de entrada, consulte [implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

O `aciDeploymentConfig.yml` descreve o ambiente de implantação usado para hospedar o serviço. A configuração de implantação é específica para o tipo de computação que você usa para a implantação. Nesse caso, uma instância de contêiner do Azure é usada. Para obter mais informações, consulte o [esquema de configuração de implantação](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Levará vários minutos antes que o processo de implantação seja concluído.

> [!TIP]
> Neste exemplo, as instâncias de contêiner do Azure são usadas. As implantações no ACI criam automaticamente o recurso de ACI necessário. Se, em vez disso, você fosse implantar no serviço kubernetes do Azure, deverá criar um cluster AKS antecipadamente e especificá-lo como parte do comando `az ml model deploy`. Para obter um exemplo de implantação no AKS, consulte [implantar um modelo em um cluster do serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

Após alguns minutos, informações semelhantes ao JSON a seguir são retornadas:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>O URI de Pontuação

O `scoringUri` retornado da implantação é o ponto de extremidade REST para um modelo implantado como um serviço Web. Você também pode obter esse URI usando o seguinte comando:

```azurecli-interactive
az ml service show -n myservice
```

Esse comando retorna o mesmo documento JSON, incluindo o `scoringUri`.

O ponto de extremidade REST pode ser usado para enviar dados para o serviço. Para obter informações sobre como criar um aplicativo cliente que envia dados para o serviço, consulte [consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Enviar dados para o serviço

Embora você possa criar um aplicativo cliente para chamar o ponto de extremidade, a CLI do Machine Learning fornece um utilitário que pode atuar como um cliente de teste. Use o comando a seguir para enviar dados no arquivo de `testdata.json` para o serviço:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se você usar o PowerShell, use o seguinte comando em vez disso:
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

A resposta do comando é semelhante a `[ 3 ]`.

## <a name="clean-up-resources"></a>Limpar recursos

> [!IMPORTANT]
> Os recursos que criou podem ser utilizados como pré-requisitos para outros tutoriais e artigos de procedimentos do Azure Machine Learning.

### <a name="delete-deployed-service"></a>Excluir serviço implantado

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja livrar-se do serviço implantado para reduzir os custos, use o seguinte comando:

```azurecli-interactive
az ml service delete -n myservice
```

Esse comando retorna um documento JSON que contém o nome do serviço excluído. Pode levar vários minutos para que o serviço seja excluído.

### <a name="delete-the-training-compute"></a>Excluir a computação de treinamento

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja se livrar do destino de computação `cpu-compute` criado para treinamento, use o seguinte comando:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Esse comando retorna um documento JSON que contém a ID do destino de computação excluído. Pode levar vários minutos para que o destino de computação seja excluído.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos que criou, exclua-os para não incorrer em encargos adicionais.

Para excluir o grupo de recursos e todos os recursos do Azure criados neste documento, use o comando a seguir. Substitua `<resource-group-name>` pelo nome do grupo de recursos que você criou anteriormente:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Passos seguintes

Neste Azure Machine Learning tutorial, você usou a CLI do Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão do Machine Learning
> * Criar um espaço de trabalho do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de registros usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar dados usando o serviço Web

Para obter mais informações sobre como usar a CLI, consulte [usar a extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
