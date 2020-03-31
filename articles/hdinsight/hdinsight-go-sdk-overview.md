---
title: Azure HDInsight SDK para ir
description: Material de referência para a utilização de clusters Azure HDInsight SDK para clusters Go e Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479591"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK para ir (Pré-visualização)

## <a name="overview"></a>Descrição geral
O HDInsight SDK for Go fornece classes e funções que lhe permitem gerir os seus clusters HDInsight. Inclui operações para criar, eliminar, atualizar, listar, redimensionar, executar ações de script, monitorizar, obter propriedades de clusters HDInsight, e muito mais.

> [!NOTE]  
>O material de referência GoDoc para este SDK também está [disponível aqui](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [ `go get` ferramenta.](https://github.com/golang/go/wiki/GoGetTools)
* [Vão embora.](https://golang.org/dl/)

## <a name="sdk-installation"></a>Instalação SDK

A partir da sua localização GOPATH, corra`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autenticação

O SDK precisa primeiro de ser autenticado com a sua subscrição Azure.  Siga o exemplo abaixo para criar um diretor de serviço e use-o para autenticar. Depois de feito, terá uma instância `ClustersClient`de a, que contém muitas funções (descritas em secções abaixo) que podem ser usadas para realizar operações de gestão.

> [!NOTE]  
> Existem outras formas de autenticar além do exemplo abaixo que poderia ser mais adequado para as suas necessidades. Todas as funções estão descritas aqui: Funções de [autenticação no SDK Azure para Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exemplo de autenticação usando um diretor de serviço

Primeiro, inicie sessão na [Azure Cloud Shell.](https://shell.azure.com/bash) Verifique se está a utilizar a subscrição na qual pretende que o diretor de serviço seja criado.

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
> Se ainda não registou o Fornecedor de Recursos HDInsight por outra função (como por exemplo, através da criação de um Cluster HDInsight através do portal Azure), tem de o fazer uma vez antes de poder autenticar. Isto pode ser feito a partir da Casca de [Nuvem Azure](https://shell.azure.com/bash) executando o seguinte comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Em seguida, escolha um nome para o seu diretor de serviço e crie-o com o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

As informações principais do serviço são apresentadas como JSON.

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

Copie o corte abaixo `TENANT_ID`e `CLIENT_ID` `CLIENT_SECRET`preencha, e `SUBSCRIPTION_ID` com as cordas do JSON que foi devolvida após executar o comando para criar o diretor de serviço.

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
> Esta secção pressupõe que já tenha `ClusterClient` autenticado e construído um `client`exemplo e armazená-lo numa variável chamada . As instruções para autenticação `ClusterClient` e obtenção de um podem ser encontradas na secção autenticação acima.

### <a name="create-a-cluster"></a>Criar um cluster

Um novo cluster pode `client.Create()`ser criado através da chamada. 

#### <a name="example"></a>Exemplo

Este exemplo demonstra como criar um cluster [Apache Spark](https://spark.apache.org/) com dois nós de cabeça e um nó de trabalhador.

> [!NOTE]  
> Primeiro é necessário criar uma Conta de Grupo de Recursos e Armazenamento, como explicado abaixo. Se já criou estes, pode saltar estes passos.

##### <a name="creating-a-resource-group"></a>Criar um grupo de recursos

Você pode criar um grupo de recursos usando a [Casca de Nuvem Azure](https://shell.azure.com/bash) executando

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Criar uma conta de armazenamento

Pode criar uma conta de armazenamento utilizando a [Casca de Nuvem Azure](https://shell.azure.com/bash) executando:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Agora executa o seguinte comando para obter a chave para a sua conta de armazenamento (você precisará disto para criar um cluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
O snippet abaixo go cria um aglomerado de faíscas com dois nós de cabeça e um nó de trabalhador. Preencha as variáveis em branco, conforme explicado nos comentários, e sinta-se à vontade para alterar outros parâmetros de acordo com as suas necessidades específicas.

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

Pode suster `get` que criou com sucesso o seu cluster.

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

### <a name="list-clusters"></a>Aglomerados de listas

#### <a name="list-clusters-under-the-subscription"></a>Lista de clusters ao abrigo da subscrição

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Lista de clusters por grupo de recursos

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Ambos `List()` `ListByResourceGroup()` e `ClusterListResultPage` devolver uma estrutura. Para obter a próxima página, pode ligar. `Next()` Isto pode ser `ClusterListResultPage.NotDone()` `false`repetido até devoluções , como mostra o exemplo abaixo.

#### <a name="example"></a>Exemplo

O exemplo que se segue imprime as propriedades de todos os clusters para a subscrição atual:

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

### <a name="update-cluster-tags"></a>Etiquetas de cluster de atualização

Pode atualizar as etiquetas de um determinado cluster como:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Exemplo

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Cluster de redimensionar

Pode redimensionar o número de nós dos trabalhadores de um determinado cluster especificando um novo tamanho como o seguinte:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorização de cluster

O HDInsight Management SDK também pode ser usado para gerir a monitorização dos seus clusters através da Suite de Gestão de Operações (OMS).

Da mesma forma `ClusterClient` que criou para usar para operações de gestão, precisa de criar um `ExtensionClient` uso para monitorizar as operações. Uma vez concluída a secção de Autenticação acima, pode criar um `ExtensionClient` like so:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de monitorização abaixo assumem `extClient` que `Authorizer` já rubricaram uma `ExtensionClient` chamada e definiram a sua como mostrado acima.

### <a name="enable-oms-monitoring"></a>Ativar a monitorização de OMS

> [!NOTE]  
> Para ativar a monitorização da OMS, deve ter um espaço de trabalho existente no Log Analytics. Se ainda não criou um, pode aprender a fazê-lo aqui: Crie um espaço de [trabalho log Analytics no portal Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Para ativar a monitorização da OMS no seu cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Ver estado da monitorização da OMS

Para obter o estatuto de OMS no seu cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Monitorização de OMS de sabilagem

Para desativar o OMS no seu cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Ações de script

O HDInsight fornece uma função de configuração chamada ações de script que invoca scripts personalizados para personalizar o cluster.

> [!NOTE]  
> Mais informações sobre como usar as ações do script podem ser encontradas aqui: [Personalize os clusters HDInsight baseados em Linux usando ações de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Executar ações de script

Pode executar ações de script num determinado cluster como:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Para as operações 'Eliminar a Ação de Script' e 'List Persisted Script Actions', é necessário criar um `ScriptActionsClient`, semelhante à forma como criou `ClusterClient` para utilizar para operações de gestão. Uma vez concluída a secção de Autenticação acima, pode criar um `ScriptActionsClient` like so:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> As ações abaixo do script exemplos `ScriptActionsClient` assumem que você já inicializou um chamado `scriptActionsClient` e definiu o seu `Authorizer` como mostrado acima.

### <a name="delete-script-action"></a>Eliminar a ação do script

Para eliminar uma ação de guião determinada e persistente num determinado cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista persistia em ações de script

> [!NOTE]  
> Ambos `ListByCluster()` devolvem uma `ScriptActionsListPage` estrutura. Para obter a próxima página, pode ligar. `Next()` Isto pode ser `ClusterListResultPage.NotDone()` `false`repetido até devoluções , como mostra o exemplo abaixo.

Para enumerar todas as ações de script persistidas para o cluster especificado:
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

### <a name="list-all-scripts-execution-history"></a>Enumerar o histórico de execução de todos os scripts

Para esta operação, é `ScriptExecutionHistoryClient`necessário criar um , `ClusterClient` à semelhança da forma como criou para utilizar para operações de gestão. Uma vez concluída a secção de Autenticação acima, pode criar um `ScriptActionsClient` like so:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O seguinte assume que já `ScriptExecutionHistoryClient` rubricaram um chamado `scriptExecutionHistoryClient` e definiram o seu `Authorizer` como mostrado acima.

Para enumerar o histórico de execução de todos os scripts para o cluster especificado:

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

Explore o [material de referência GoDoc.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) Os GoDocs fornecem documentação de referência para todas as funções no SDK.
