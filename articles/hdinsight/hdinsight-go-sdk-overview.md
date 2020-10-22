---
title: Azure HDInsight SDK para Go
description: Material de referência para a utilização de clusters Azure HDInsight SDK para aglomerados Go e Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 162049c12c0618298695e43dae43f16e9fb50260
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372196"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK para Go (Pré-visualização)

## <a name="overview"></a>Descrição geral
O HDInsight SDK for Go fornece aulas e funções que lhe permitem gerir os seus clusters HDInsight. Inclui operações para criar, eliminar, atualizar, listar, redimensionar, executar ações de script, monitorizar, obter propriedades de clusters HDInsight, e muito mais.

> [!NOTE]  
>O material de referência GoDoc para este SDK também está [disponível aqui.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [ `go get` ferramenta.](https://github.com/golang/go/wiki/GoGetTools)
* [Vai](https://golang.org/dl/)embora.

## <a name="sdk-installation"></a>Instalação SDK

A partir da sua localização GOPATH, corra `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autenticação

O SDK precisa primeiro de ser autenticado com a sua subscrição Azure.  Siga o exemplo abaixo para criar um principal de serviço e use-o para autenticar. Depois disso, terá um exemplo de um `ClustersClient` , que contém muitas funções (delineadas em secções abaixo) que podem ser usadas para executar operações de gestão.

> [!NOTE]  
> Existem outras formas de autenticar além do exemplo abaixo que poderia potencialmente ser mais adequado para as suas necessidades. Todas as funções estão delineadas aqui: [Funções de autenticação no Azure SDK for Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exemplo de autenticação usando um principal de serviço

Primeiro, faça login na [Azure Cloud Shell](https://shell.azure.com/bash). Verifique se está a utilizar a subscrição na qual pretende que o principal do serviço seja criado.

```azurecli-interactive
az account show
```

As suas informações de subscrição são apresentadas como JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Se não estiver registado na subscrição correta, selecione a correta executando: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Se ainda não registou o Fornecedor de Recursos HDInsight por outra função (como por exemplo, criar um Cluster HDInsight através do portal Azure), tem de o fazer uma vez antes de poder autenticar. Isto pode ser feito a partir da [Azure Cloud Shell](https://shell.azure.com/bash) executando o seguinte comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Em seguida, escolha um nome para o seu principal de serviço e crie-o com o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

A informação principal do serviço é apresentada como JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Copie o corte abaixo e preencha, `TENANT_ID` , e com as cordas do `CLIENT_ID` `CLIENT_SECRET` `SUBSCRIPTION_ID` JSON que foi devolvido após executar o comando para criar o principal de serviço.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Gestão de clusters

> [!NOTE]  
> Esta secção pressupõe que já autenticou e construiu um `ClusterClient` caso e guardou-o numa variável chamada `client` . As instruções para autenticação e obtenção de uma `ClusterClient` pode ser encontrada na secção de Autenticação acima.

### <a name="create-a-cluster"></a>Criar um cluster

Um novo cluster pode ser criado `client.Create()` chamando. 

#### <a name="example"></a>Exemplo

Este exemplo demonstra como criar um aglomerado [apache spark](https://spark.apache.org/) com dois nós de cabeça e um nó de trabalhador.

> [!NOTE]  
> Primeiro, é necessário criar um Grupo de Recursos e Uma Conta de Armazenamento, conforme explicado abaixo. Se já os criou, pode saltar estes passos.

##### <a name="creating-a-resource-group"></a>Criar um grupo de recursos

Pode criar um grupo de recursos utilizando o [Azure Cloud Shell](https://shell.azure.com/bash) executando

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Criar uma conta de armazenamento

Pode criar uma conta de armazenamento utilizando a [Azure Cloud Shell](https://shell.azure.com/bash) executando:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Agora executar o seguinte comando para obter a chave para a sua conta de armazenamento (você precisará disso para criar um cluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
O snippet abaixo go cria um cluster spark com dois nós de cabeça e um nó de trabalhador. Preencha as variáveis em branco como explicado nos comentários e sinta-se livre para alterar outros parâmetros de acordo com as suas necessidades específicas.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Obtenha detalhes do cluster

Para obter propriedades para um determinado cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Pode usar `get` para confirmar que criou com sucesso o seu cluster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A saída deve parecer:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Agrupamentos de listas

#### <a name="list-clusters-under-the-subscription"></a>Lista de aglomerados sob a subscrição

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Listar clusters por grupo de recursos

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Ambos `List()` e devolver uma `ListByResourceGroup()` `ClusterListResultPage` estrutura. Para obter a próxima página, pode `Next()` ligar. Isto pode ser repetido até `ClusterListResultPage.NotDone()` devoluções `false` , como mostra o exemplo abaixo.

#### <a name="example"></a>Exemplo

O exemplo a seguir imprime as propriedades de todos os clusters para a subscrição atual:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Eliminar um cluster

Para eliminar um cluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Atualizar tags de cluster

Pode atualizar as etiquetas de um determinado cluster assim:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Exemplo

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Redimensionar o aglomerado

Pode redimensionar o número de nós de trabalhadores de um determinado cluster especificando um novo tamanho como este:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorização de cluster

O HDInsight Management SDK também pode ser usado para gerir a monitorização dos seus clusters através do Suite de Gestão de Operações (OMS).

Da mesma forma que criou `ClusterClient` para usar para operações de gestão, precisa criar um `ExtensionClient` para usar para operações de monitorização. Uma vez concluída a secção de Autenticação acima, pode criar uma `ExtensionClient` seguinte forma:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de monitorização abaixo pressupõem que já iniciaisizou um `ExtensionClient` chamado `extClient` e definiu o seu como mostrado `Authorizer` acima.

### <a name="enable-oms-monitoring"></a>Permitir a monitorização do OMS

> [!NOTE]  
> Para ativar o controlo OMS, tem de ter um espaço de trabalho log analytics existente. Se ainda não criou um, pode aprender a fazê-lo aqui: [Criar um espaço de trabalho Log Analytics no portal Azure.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)

Para ativar a monitorização do OMS no seu cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Ver estado da monitorização da OMS

Para obter o estado de OMS no seu cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Desativar a monitorização do OMS

Para desativar o OMS no seu cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Ações de script

O HDInsight fornece uma função de configuração chamada ações de script que invoca scripts personalizados para personalizar o cluster.

> [!NOTE]  
> Mais informações sobre como usar as ações de script podem ser encontradas aqui: [Personalize clusters HDInsight baseados em Linux usando ações de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Executar ações de script

Pode executar ações de script num determinado cluster assim:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Para as operações 'Delete Script Action' e 'List Persisted Script Actions', precisa de criar um `ScriptActionsClient` , semelhante à forma como criou para usar para `ClusterClient` operações de gestão. Uma vez concluída a secção de Autenticação acima, pode criar um `ScriptActionsClient` semelhante:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de ações de script abaixo assumem que já iniciaisizou um `ScriptActionsClient` chamado `scriptActionsClient` e definiu o seu como mostrado `Authorizer` acima.

### <a name="delete-script-action"></a>Apagar ação do script

Para eliminar uma ação de script persistiu especificada num determinado cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista de ações de scripts persistentes

> [!NOTE]  
> Ambos `ListByCluster()` devolvem uma `ScriptActionsListPage` estrutura. Para obter a próxima página, pode `Next()` ligar. Isto pode ser repetido até `ClusterListResultPage.NotDone()` devoluções `false` , como mostra o exemplo abaixo.

Para listar todas as ações de scripts persistidos para o cluster especificado:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Listar o histórico de execução de todos os scripts

Para esta operação, é necessário criar um `ScriptExecutionHistoryClient` , à semelhança do que criou para utilizar para `ClusterClient` operações de gestão. Uma vez concluída a secção de Autenticação acima, pode criar um `ScriptActionsClient` semelhante:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O abaixo assume que já iniciais um `ScriptExecutionHistoryClient` chamado e definir o seu como mostrado `scriptExecutionHistoryClient` `Authorizer` acima.

Para listar o histórico de execução de todos os scripts para o cluster especificado:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Este exemplo imprime todos os detalhes para todas as execuções de scripts anteriores.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Explore o [material de referência GoDoc.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) Os GoDocs fornecem documentação de referência para todas as funções do SDK.
