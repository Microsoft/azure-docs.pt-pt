---
title: Treinar e implantar modelos da CLI
titleSuffix: Azure Machine Learning service
description: Saiba como usar a extensão do Machine Learning para CLI do Azure para treinar, registrar e implantar um modelo na linha de comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350417"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: Treinar e implantar um modelo da CLI

Neste tutorial, você usa a extensão de aprendizado de máquina para o CLI do Azure para treinar, registrar e implantar um modelo.

Os scripts de treinamento do Python neste tutorial usam o [scikit-Learn](https://scikit-learn.org/) para treinar um modelo básico. O foco deste tutorial não está nos scripts ou no modelo, mas o processo de usar a CLI para trabalhar com o Azure Machine Learning.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Instalar a extensão do Machine Learning
> * Criar um espaço de trabalho do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar dados usando o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usar o [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="download-the-example-project"></a>Baixar o projeto de exemplo

Para este tutorial, baixe o projeto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . Os arquivos nos diretórios `model-training` e `model-deployment` são usados pelas etapas deste tutorial.

Para obter uma cópia local dos arquivos, [Baixe um arquivo. zip](https://github.com/microsoft/MLOps/archive/master.zip)ou use o comando git a seguir para clonar o repositório:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Arquivos de treinamento

O diretório `model-training` contém os seguintes arquivos, que são usados ao treinar um modelo:

* `.azureml\sklearn.runconfig`: Um arquivo de __configuração de execução__ . Esse arquivo define o ambiente de tempo de execução necessário para treinar o modelo.
* `train-sklearn.py`: O script de treinamento. Esse arquivo treina o modelo.
* `mylib.py`: Um módulo auxiliar, usado pelo `train-sklearn.py`.
* `training-env.yml`: Define as dependências de software necessárias para executar o script de treinamento.

O script de treinamento usa o conjunto de informações diabetes fornecido com o scikit-Learn para treinar um modelo.

### <a name="deployment-files"></a>Arquivos de implantação

O diretório `model-deployment` contém os seguintes arquivos, que são usados para implantar o modelo treinado como um serviço Web:

* `aciDeploymentConfig.yml`: Um arquivo de __configuração de implantação__ . Esse arquivo define o ambiente de hospedagem necessário para o modelo.
* `inferenceConfig.yml`: Um arquivo configuration__ de inferência. Esse arquivo define o ambiente de software usado pelo serviço para pontuar dados com o modelo.
* `score.py`: Um script Python que aceita dados de entrada, pontua-os usando o modelo e, em seguida, retorna uma resposta.
* `scoring-env.yml`: As dependências Conda necessárias para executar o modelo e o script `score.py`.

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais básico é autenticar-se interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) navegação e a inserção de um código de autorização.

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

Um grupo de recursos é um contêiner básico de recursos na plataforma Azure. Ao trabalhar com o serviço de Azure Machine Learning, o grupo de recursos conterá seu espaço de trabalho de serviço do Azure Machine Learning. Ele também conterá outros serviços do Azure usados pelo espaço de trabalho. Por exemplo, se você treinar seu modelo usando um recurso de computação baseado em nuvem, esse recurso será criado no grupo de recursos.

Para __criar um novo grupo de recursos__, use o comando a seguir. Substituir `<resource-group-name>` pelo nome a ser usado para este grupo de recursos. Substitua `<location>` pela região do Azure a ser usada para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região onde o serviço de Azure Machine Learning está disponível. Para obter informações, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

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

## <a name="create-a-workspace"></a>Criar uma área de trabalho

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

Em um terminal ou prompt de comando, use os seguintes comandos para alterar os diretórios para o diretório `mlops` e, em seguida, conecte-se ao seu espaço de trabalho:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Este comando cria um arquivo `.azureml/config.json`, que contém as informações necessárias para se conectar ao seu espaço de trabalho. O restante dos comandos `az ml` usados neste tutorial usará esse arquivo, de modo que você não precisa adicionar o espaço de trabalho e o grupo de recursos a todos os comandos.

## <a name="create-the-compute-target-for-training"></a>Criar o destino de computação para treinamento

Este exemplo usa uma instância de computação Azure Machine Learning para treinar o modelo. Para criar uma nova instância de computação, use o seguinte comando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Esse comando cria um novo destino de computação chamado `cpu`, com um máximo de quatro nós. O tamanho da VM selecionado fornece uma VM com um recurso de GPU. Para obter informações sobre o tamanho da VM, consulte [tipos e tamanhos de VM].

> [!IMPORTANT]
> O nome do destino de computação (`cpu` nesse caso), é importante; Ele é referenciado pelo arquivo `.azureml/sklearn.runconfig` usado na próxima seção.

## <a name="submit-the-training-run"></a>Enviar a execução de treinamento

Para iniciar uma execução de treinamento no destino de computação `cpu`, altere os diretórios para o diretório `model-training` e, em seguida, use o seguinte comando:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Este comando especifica um nome para o experimento (`myexperiment`). O experimento armazena informações sobre essa execução no espaço de trabalho.

O parâmetro `-c sklearn` especifica o arquivo `.azureml/sklearn.runconfig`. Como mencionado anteriormente, esse arquivo contém informações usadas para configurar o ambiente usado pela execução de treinamento. Se você inspecionar esse arquivo, verá que ele faz referência ao destino de computação `cpu` que você criou anteriormente. Ele também lista o número de nós a serem usados ao treinar (`"nodeCount": "4"`) e contém uma seção `"condaDependenciees"` que lista os pacotes do python necessários para executar o script de treinamento.

Para obter mais informações sobre arquivos de configuração de execução, consulte [configurar e usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli).

O parâmetro `-t` armazena uma referência a essa execução em um arquivo JSON e será usado nas próximas etapas para registrar e baixar o modelo.

Como os processos de execução de treinamento, ele transmite informações da sessão de treinamento no recurso de computação remota. Parte das informações é semelhante ao seguinte texto:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Esse texto é registrado no script de treinamento (`train-sklearn.py`) e exibe duas das métricas de desempenho para esse modelo. Nesse caso, queremos que o modelo tenha o valor alfa mais alto. As métricas de desempenho são específicas para o modelo que você está treinando. Outros modelos terão métricas de desempenho diferentes.

Se você inspecionar o `train-sklearn.py`, observará que ele também usa o valor alfa ao armazenar os modelos treinados para o arquivo. Nesse caso, ele treina vários modelos. Aquele com o alfa mais alto deve ser o melhor. Observando a saída acima e o código, o modelo com um alfa de 0,95 foi salvo como `./outputs/ridge_0.95.pkl`

O modelo foi salvo no diretório `./outputs` no destino de computação em que foi treinado. Nesse caso, a instância de computação Azure Machine Learning na nuvem do Azure. O processo de treinamento carrega automaticamente o conteúdo do diretório `./outputs` do destino de computação em que o treinamento ocorre em seu espaço de trabalho do Azure Machine Learning. Ele é armazenado como parte do experimento (`myexperiment` neste exemplo).

## <a name="register-the-model"></a>Registe o modelo

Para registrar o modelo diretamente da versão armazenada em seu experimento, use o seguinte comando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Esse comando registra o arquivo `outputs/ridge_0.95.pkl` criado pela execução de treinamento como um novo registro de modelo denominado `mymodel`. O `--assets-path` faz referência a um caminho em um experimento. Nesse caso, as informações de teste e execução são carregadas do arquivo `runoutput.json` criado pelo comando de treinamento. O `-t registeredmodel.json` cria um arquivo JSON que faz referência ao novo modelo registrado criado por esse comando e é usado por outros comandos da CLI que funcionam com modelos registrados.

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
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

O primeiro comando baixa o modelo registrado no diretório atual. O nome do arquivo é `ridge_0.95.pkl`, que é o arquivo referenciado quando você registrou o modelo. O segundo comando registra o modelo local (`-p "ridge_0.95.pkl"`) com o mesmo nome do registro anterior (`mymodel`). Desta vez, os dados JSON retornados listam a versão como 2.

## <a name="deploy-the-model"></a>Implementar o modelo

Para implantar um modelo, altere os diretórios para o diretório `model-deployment` e, em seguida, use o seguinte comando:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Você pode receber a mensagem "falha ao criar o cliente do Docker". Você pode ignorar esta mensagem. A CLI pode implantar um serviço Web em um contêiner do Docker local e verificar o Docker. Nesse caso, não estamos usando uma implantação local.

Este comando implanta um novo serviço chamado `myservice`, usando a versão 1 do modelo que você registrou anteriormente.

O arquivo `inferenceConfig.yml` fornece informações sobre como executar a inferência, como o script de entrada (`score.py`) e as dependências de software. Para obter mais informações sobre a estrutura desse arquivo, consulte o [esquema de configuração de inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema). Para obter mais informações sobre scripts de entrada, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

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

Embora você possa criar um aplicativo cliente para chamar o ponto de extremidade, a CLI do Machine Learning fornece um utilitário que pode atuar como um cliente de teste. Use o comando a seguir para enviar dados de teste para o serviço:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

A resposta do comando é semelhante a `[4684.920839774082]`.

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

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja livrar-se do destino de computação `cpu` criado para treinamento, use o seguinte comando:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Esse comando retorna um documento JSON que contém a ID do destino de computação excluído. Pode levar vários minutos para que o destino de computação seja excluído.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos que criou, exclua-os para não incorrer em encargos adicionais.

Para excluir o grupo de recursos e todos os recursos do Azure criados neste documento, use o comando a seguir. Substitua `<resource-group-name>` pelo nome do grupo de recursos criado anteriormente:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Passos seguintes

Neste Azure Machine Learning tutorial, você usou a CLI do Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão do Machine Learning
> * Criar um espaço de trabalho do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Iniciar uma execução de treinamento
> * Registrar e baixar um modelo
> * Implantar o modelo como um serviço Web
> * Pontuar dados usando o serviço Web

Para obter mais informações sobre como usar a CLI, consulte [usar a extensão da CLI para o serviço de Azure Machine Learning](reference-azure-machine-learning-cli.md).