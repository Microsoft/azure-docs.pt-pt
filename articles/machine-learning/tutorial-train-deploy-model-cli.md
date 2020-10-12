---
title: Treinar e implementar modelos a partir do CLI
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar a extensão de machine learning para o Azure CLI treinar, registar e implementar um modelo a partir da linha de comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 294a3dc9525ba0a0c533d68027b148ad402cd7ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268847"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: Treine e implemente um modelo a partir do CLI


Neste tutorial, você usa a extensão de machine learning para o CLI Azure para treinar, registar e implementar um modelo.

Os scripts de treino Python neste tutorial usam [scikit-learn](https://scikit-learn.org/) para treinar um modelo simples. O foco deste tutorial não está nos scripts ou no modelo, mas no processo de utilização do CLI para trabalhar com a Azure Machine Learning.

Saiba como tomar as seguintes ações:

> [!div class="checklist"]
> * Instale a extensão de aprendizagem automática
> * Criar uma área de trabalho do Azure Machine Learning
> * Criar o recurso computacional usado para treinar o modelo
> * Definir e registar o conjunto de dados utilizado para treinar o modelo
> * Inicie uma corrida de treino
> * Registar e descarregar um modelo
> * Implementar o modelo como um serviço web
> * Obter dados usando o serviço web

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** necessita do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    Se utilizar o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acedido através do navegador e vive na nuvem.

## <a name="download-the-example-project"></a>Descarregue o projeto exemplo

Para este tutorial, descarregue o [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projeto. Os ficheiros do `examples/cli-train-deploy` diretório são usados pelos passos deste tutorial.

Para obter uma cópia local dos ficheiros, faça o download de [um arquivo .zip,](https://github.com/microsoft/MLOps/archive/master.zip)ou use o seguinte comando Git para clonar o repositório:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Ficheiros de formação

O `examples/cli-train-deploy` diretório do projeto contém os seguintes ficheiros, que são utilizados na formação de um modelo:

* `.azureml\mnist.runconfig`: Um ficheiro __de configuração de execução.__ Este ficheiro define o ambiente de tempo de funcionamento necessário para treinar o modelo. Neste exemplo, também monta os dados utilizados para treinar o modelo no ambiente de formação.
* `scripts\train.py`: O roteiro do treino. Este ficheiro treina o modelo.
* `scripts\utils.py`: Um ficheiro de ajuda utilizado pelo script de treino.
* `.azureml\conda_dependencies.yml`: Define as dependências de software necessárias para executar o roteiro de treino.
* `dataset.json`: A definição do conjunto de dados. Usado para registar o conjunto de dados do MNIST no espaço de trabalho Azure Machine Learning.

### <a name="deployment-files"></a>Ficheiros de implantação

O repositório contém os seguintes ficheiros, que são utilizados para implantar o modelo treinado como um serviço web:

* `aciDeploymentConfig.yml`: Um ficheiro __de configuração de implantação.__ Este ficheiro define o ambiente de hospedagem necessário para o modelo.
* `inferenceConfig.json`: Um ficheiro __de configuração de inferência.__ Este ficheiro define o ambiente de software utilizado pelo serviço para marcar dados com o modelo.
* `score.py`: Um script python que aceita dados de entrada, pontua-os usando o modelo e, em seguida, devolve uma resposta.
* `scoring-env.yml`: As dependências da conda necessárias para executar o modelo e `score.py` o script.
* `testdata.json`: Um ficheiro de dados que pode ser utilizado para testar o serviço web implantado.

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Existem várias formas de autenticar a sua subscrição Azure a partir do CLI. O mais simples é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e utilize o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir um browser e seguir as instruções na linha de comando. As instruções envolvem navegar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>Instale a extensão de aprendizagem automática

Para instalar a extensão de aprendizagem automática, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Se receber uma mensagem de que a extensão já está instalada, utilize o seguinte comando para atualizar para a versão mais recente:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor de recursos na plataforma Azure. Ao trabalhar com o Azure Machine Learning, o grupo de recursos conterá o seu espaço de trabalho Azure Machine Learning. Também conterá outros serviços Azure utilizados pelo espaço de trabalho. Por exemplo, se treinar o seu modelo utilizando um recurso computacional baseado na nuvem, esse recurso é criado no grupo de recursos.

Para __criar um novo grupo de recursos,__ utilize o seguinte comando. `<resource-group-name>`Substitua-o pelo nome a utilizar para este grupo de recursos. `<location>`Substitua-a pela região Azure para utilizar para este grupo de recursos:

> [!TIP]
> Deve selecionar uma região onde está disponível o Azure Machine Learning. Para obter informações, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta deste comando é semelhante à seguinte JSON:

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

Para obter mais informações sobre o trabalho com grupos de recursos, consulte [o grupo AZ](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest&preserve-view=true).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar um novo espaço de trabalho, utilize o seguinte comando. `<workspace-name>`Substitua-o pelo nome que pretende utilizar para este espaço de trabalho. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante à seguinte JSON:

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

## <a name="connect-local-project-to-workspace"></a>Ligue o projeto local ao espaço de trabalho

A partir de um terminal ou de um pedido de comando, utilize os seguintes diretórios de troca de comandos para o `cli-train-deploy` diretório e, em seguida, ligue-se ao seu espaço de trabalho:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Este comando cria um `.azureml/config.json` ficheiro, que contém informações necessárias para ligar ao seu espaço de trabalho. O resto dos `az ml` comandos utilizados neste tutorial usará este ficheiro, para que não tenha de adicionar o espaço de trabalho e o grupo de recursos a todos os comandos.

## <a name="create-the-compute-target-for-training"></a>Criar o alvo do cálculo para a formação

Este exemplo utiliza um cluster Azure Machine Learning Compute para treinar o modelo. Para criar um novo cluster de cálculo, utilize o seguinte comando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A saída deste comando é semelhante à seguinte JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Este comando cria um novo alvo de computação chamado `cpu-cluster` , com um máximo de quatro nós. O tamanho VM selecionado fornece um VM com um recurso GPU. Para obter informações sobre o tamanho do VM, consulte [tipos e tamanhos VM].

> [!IMPORTANT]
> O nome do alvo do cálculo `cpu-cluster` (neste caso), é referenciado pelo `.azureml/mnist.runconfig` ficheiro utilizado na secção seguinte.

## <a name="define-the-dataset"></a>Definir o conjunto de dados

Para treinar um modelo, pode fornecer os dados de treino utilizando um conjunto de dados. Para criar um conjunto de dados a partir do CLI, tem de fornecer um ficheiro de definição de conjunto de dados. O `dataset.json` ficheiro fornecido no repo cria um novo conjunto de dados utilizando os dados do MNIST. O conjunto de dados que cria está `mnist-dataset` nomeado.

Para registar o conjunto de dados utilizando o `dataset.json` ficheiro, utilize o seguinte comando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A saída deste comando é semelhante à seguinte JSON:

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
> Copie o valor da `id` entrada, tal como é utilizado na secção seguinte.

Para ver um modelo mais abrangente para um conjunto de dados, utilize o seguinte comando:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Referenciar o conjunto de dados

Para disponibilizar o conjunto de dados no ambiente de treino, deve fazê-lo referenciar a partir do ficheiro runconfig. O `.azureml/mnist.runconfig` ficheiro contém as seguintes entradas YAML:

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

Altere o valor da `id` entrada para corresponder ao valor devolvido quando registou o conjunto de dados. Este valor é utilizado para carregar os dados no alvo do cálculo durante o treino.

Este YAML resulta nas seguintes ações durante o treino:

* Monta o conjunto de dados (com base no ID do conjunto de dados) no ambiente de treino e armazena o caminho até ao ponto de montagem na `mnist` variável ambiente.
* Passa a localização dos dados (ponto de montagem) dentro do ambiente de treino para o script usando o `--data-folder` argumento.

O ficheiro runconfig também contém informações usadas para configurar o ambiente utilizado pelo treino. Se inspecionar este ficheiro, verá que faz referência ao `cpu-compute` alvo de cálculo que criou anteriormente. Também lista o número de nós a utilizar durante o treino `"nodeCount": "4"` (), e contém uma `"condaDependencies"` secção que lista os pacotes Python necessários para executar o script de treino.

> [!TIP]
> Embora seja possível criar manualmente um ficheiro runconfig, o deste exemplo foi criado usando o `generate-runconfig.py` ficheiro incluído no repositório. Este ficheiro obtém uma referência ao conjunto de dados registado, cria uma execução config programaticamente, e depois persiste em arquivá-lo.

Para obter mais informações sobre ficheiros de configuração de execução, consulte [utilize alvos de computação para a formação de modelos](how-to-set-up-training-targets.md#whats-a-run-configuration). Para obter uma referência completa ao JSON, consulte a [runconfigschema.jsem](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Submeta a formação

Para iniciar uma corrida de treino no alvo do `cpu-cluster` cálculo, utilize o seguinte comando:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Este comando especifica um nome para a experiência ( `myexperiment` ). A experiência armazena informações sobre esta corrida no espaço de trabalho.

O `-c mnist` parâmetro especifica o `.azureml/mnist.runconfig` ficheiro.

O `-t` parâmetro armazena uma referência a esta execução num ficheiro JSON, e será usado nos próximos passos para registar e descarregar o modelo.

À medida que o treino processa, transmite informação da sessão de treino sobre o recurso de computação remota. Parte da informação é semelhante ao seguinte texto:

```output
Predict the test set
Accuracy is 0.9185
```

Este texto é registado a partir do script de treino e apresenta a precisão do modelo. Outros modelos terão métricas de desempenho diferentes.

Se inspecionar o script de treino, notará que também utiliza o valor alfa quando armazena o modelo treinado para `outputs/sklearn_mnist_model.pkl` .

O modelo foi guardado para o `./outputs` diretório no alvo do cálculo onde foi treinado. Neste caso, o Azure Machine Learning Compute na nuvem Azure. O processo de formação envia automaticamente o conteúdo do `./outputs` diretório a partir do alvo do cálculo onde ocorre formação para o seu espaço de trabalho Azure Machine Learning. É armazenado como parte da experiência ( `myexperiment` neste exemplo).

## <a name="register-the-model"></a>Registar o modelo

Para registar o modelo diretamente da versão armazenada na sua experiência, utilize o seguinte comando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Este comando regista o `outputs/sklearn_mnist_model.pkl` ficheiro criado pela formação como um novo modelo de registo denominado `mymodel` . As `--assets-path` referências são um caminho numa experiência. Neste caso, a experiência e a informação de execução são carregadas a partir do `runoutput.json` ficheiro criado pelo comando de treino. O `-t registeredmodel.json` cria um ficheiro JSON que faz referência ao novo modelo registado criado por este comando, e é utilizado por outros comandos CLI que funcionam com modelos registados.

A saída deste comando é semelhante à seguinte JSON:

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

### <a name="model-versioning"></a>Controlo de versões de modelos

Note o número da versão devolvida para o modelo. A versão é incrementada cada vez que regista um novo modelo com este nome. Por exemplo, pode descarregar o modelo e registá-lo a partir de um ficheiro local utilizando os seguintes comandos:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

O primeiro comando descarrega o modelo registado para o diretório atual. O nome do ficheiro é `sklearn_mnist_model.pkl` , que é o ficheiro referenciado quando registou o modelo. O segundo comando regista o modelo local `-p "sklearn_mnist_model.pkl"` com o mesmo nome que o registo anterior `mymodel` . Desta vez, os dados da JSON devolvidos listam a versão como 2.

## <a name="deploy-the-model"></a>Implementar o modelo

Para implementar um modelo, utilize o seguinte comando:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Pode receber um aviso sobre "Não verifique a existência do SiteWebservice" ou "Falhou na criação do cliente Docker". Pode ignorar isto com segurança, uma vez que não está a implementar um serviço web local.

Este comando implementa um novo serviço denominado `myservice` , utilizando a versão 1 do modelo que registou anteriormente.

O `inferenceConfig.yml` ficheiro fornece informações sobre como utilizar o modelo para inferência. Por exemplo, refere-se ao script de entrada `score.py` e às dependências do software.

Para obter mais informações sobre a estrutura deste ficheiro, consulte o esquema de configuração de [Inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema). Para obter mais informações sobre scripts de entrada, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Descreve `aciDeploymentConfig.yml` o ambiente de implantação utilizado para hospedar o serviço. A configuração de implementação é específica do tipo de cálculo que utiliza para a implementação. Neste caso, é utilizada uma instância de contentores Azure. Para obter mais informações, consulte o [esquema de configuração de implementação](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Levará alguns minutos até que o processo de implantação esteja concluído.

> [!TIP]
> Neste exemplo, são utilizadas instâncias para o contentor Azure. As implementações para o ACI criam automaticamente o recurso ACI necessário. Se em vez disso, se deslocar para o Serviço Azure Kubernetes, tem de criar um cluster AKS com antecedência e especi-lo como parte do `az ml model deploy` comando. Para um exemplo de implantação para AKS, consulte [implementar um modelo para um cluster de serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Após vários minutos, são devolvidas informações semelhantes às seguintes JSON:

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

### <a name="the-scoring-uri"></a>O URI pontuante

O `scoringUri` devolvido da implementação é o ponto final REST para um modelo implantado como serviço web. Também pode obter este URI utilizando o seguinte comando:

```azurecli-interactive
az ml service show -n myservice
```

Este comando devolve o mesmo documento JSON, incluindo o `scoringUri` .

O ponto final REST pode ser utilizado para enviar dados para o serviço. Para obter informações sobre a criação de uma aplicação de cliente que envia dados para o serviço, consulte [Consumir um modelo de Aprendizagem automática Azure implementado como um serviço web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Enviar dados para o serviço

Enquanto pode criar uma aplicação de cliente para ligar para o ponto final, o CLI de machine learning fornece um utilitário que pode funcionar como um cliente de teste. Utilize o seguinte comando para enviar dados no `testdata.json` ficheiro para o serviço:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se utilizar o PowerShell, utilize o seguinte comando:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

A resposta do comando é semelhante a `[ 3 ]` .

## <a name="clean-up-resources"></a>Limpar os recursos

> [!IMPORTANT]
> Os recursos que criou podem ser utilizados como pré-requisitos para outros tutoriais e artigos de procedimentos do Azure Machine Learning.

### <a name="delete-deployed-service"></a>Eliminar serviço implementado

Se planeia continuar a utilizar o espaço de trabalho Azure Machine Learning, mas pretender livrar-se do serviço implantado para reduzir custos, utilize o seguinte comando:

```azurecli-interactive
az ml service delete -n myservice
```

Este comando devolve um documento JSON que contém o nome do serviço eliminado. Pode demorar alguns minutos até que o serviço seja apagado.

### <a name="delete-the-training-compute"></a>Eliminar o computo de formação

Se planeia continuar a utilizar o espaço de trabalho Azure Machine Learning, mas se quiser livrar-se do `cpu-cluster` alvo de computação criado para treinar, utilize o seguinte comando:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Este comando devolve um documento JSON que contém o ID do alvo de computação eliminado. Pode demorar alguns minutos até que o alvo do cálculo seja eliminado.

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar os recursos que criou, elimine-os para não incorrer em custos adicionais.

Para eliminar o grupo de recursos e todos os recursos Azure criados neste documento, utilize o seguinte comando. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos que criou anteriormente:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de Aprendizagem automática Azure, utilizou o CLI de aprendizagem automática para as seguintes tarefas:

> [!div class="checklist"]
> * Instale a extensão de aprendizagem automática
> * Criar uma área de trabalho do Azure Machine Learning
> * Criar o recurso computacional usado para treinar o modelo
> * Definir e registar o conjunto de dados utilizado para treinar o modelo
> * Inicie uma corrida de treino
> * Registar e descarregar um modelo
> * Implementar o modelo como um serviço web
> * Obter dados usando o serviço web

Para obter mais informações sobre a utilização do CLI, consulte [utilizar a extensão CLI para aprendizagem automática Azure](reference-azure-machine-learning-cli.md).
