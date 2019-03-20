---
title: SDK do Go do Azure HDInsight
description: Referência para o Azure HDInsight Go SDK
services: hdinsight
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: tyfox
ms.custom: seodec18
ms.openlocfilehash: b430c69f4ed9206b34f8ca66534aa82e85c0b240
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224792"
---
# <a name="hdinsight-go-management-sdk-preview"></a>Pré-visualização do SDK de gestão do Go do HDInsight

## <a name="overview"></a>Descrição geral
O SDK Go do HDInsight fornece classes e funções que permitem que gerencie seus clusters do HDInsight. Inclui operações para criar, eliminar, atualizar, listar, redimensionar, execute as ações de script, monitorizar e obter propriedades de clusters do HDInsight e muito mais.

> [!NOTE]  
>Material de referência do GoDoc para este SDK é também [disponível aqui](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* [Vá](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalação do SDK

A partir da sua localização GOPATH, execute `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Primeiro, o SDK precisa de ser autenticada com a sua subscrição do Azure.  Siga o exemplo abaixo para criar um principal de serviço e utilizá-lo para autenticar. Depois de o fazer, terá uma instância de um `ClustersClient`, que contém muitas funções (descritas a seguir secções) que podem ser utilizadas para efetuar operações de gestão.

> [!NOTE]  
> Existem outras formas de autenticar além do exemplo que foi potencialmente ser mais adequado às suas necessidades abaixo. Todas as funções são descritas aqui: [Funções de autenticação no Azure SDK para Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exemplo de autenticação com um principal de serviço

Primeiro, início de sessão [Azure Cloud Shell](https://shell.azure.com/bash). Certifique-se de que está a utilizar a subscrição na qual pretende que o principal de serviço criado. 

```azurecli-interactive
az account show
```

Informações da sua subscrição são apresentadas como JSON.

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

Se não estiver conectado à subscrição correta, selecione o correto ao executar: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Se ainda não registou o fornecedor de recursos do HDInsight por outra função (tal como através da criação de um Cluster do HDInsight através do portal do Azure), terá de fazê-lo uma vez antes possa ser autenticado. Isso pode ser feito a partir da [Azure Cloud Shell](https://shell.azure.com/bash) executando o seguinte comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Em seguida, escolha um nome para o seu principal de serviço e criá-la com o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

As informações do principal de serviço são apresentadas como JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copie o trecho de código e preencha abaixo `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, e `SUBSCRIPTION_ID` com as cadeias de caracteres do JSON que foi devolvida depois de executar o comando para criar o principal de serviço.

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
> Esta secção assume que já autenticado e construído um `ClusterClient` da instância e o armazenamos numa variável chamada `client`. Instruções para autenticar e obter um `ClusterClient` podem ser encontrados na secção autenticação acima.

### <a name="create-a-cluster"></a>Criar um cluster

Um novo cluster pode ser criado chamando `client.Create()`. 

#### <a name="example"></a>Exemplo

Este exemplo demonstra como criar uma [Apache Spark](https://spark.apache.org/) cluster com 2 nós principais e nó de 1 trabalho.

> [!NOTE]  
> Tem primeiro de criar um grupo de recursos e a conta de armazenamento, conforme explicado abaixo. Se já tiver criado essas, pode ignorar estes passos.

##### <a name="creating-a-resource-group"></a>Criar um grupo de recursos

Pode criar um grupo de recursos utilizando o [Azure Cloud Shell](https://shell.azure.com/bash) através da execução
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Criar uma conta de armazenamento

Pode criar uma conta de armazenamento com o [Azure Cloud Shell](https://shell.azure.com/bash) ao executar:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Execute o comando seguinte para obter a chave para a sua conta de armazenamento (irá precisar de criar um cluster) a agora:
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
O abaixo Go fragmento cria um cluster do Spark com 2 nós principais e o nó de 1 trabalho. Preencha as variáveis em branco, conforme explicado nos comentários e à vontade alterar os outros parâmetros de acordo com as suas necessidades específicas.

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

### <a name="get-cluster-details"></a>Obter os detalhes do cluster

Para obter as propriedades de um determinado cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Pode usar `get` para confirmar que criou com êxito o cluster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

O resultado deverá ser semelhante:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Listar clusters

#### <a name="list-clusters-under-the-subscription"></a>Listar clusters sob a subscrição
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Listar clusters por grupo de recursos
```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Ambos `List()` e `ListByResourceGroup()` devolver um `ClusterListResultPage` struct. Para obter a página seguinte, pode chamar `Next()`. Ela pode ser repetida até `ClusterListResultPage.NotDone()` retorna `false`, conforme mostrado no exemplo abaixo.

#### <a name="example"></a>Exemplo
O exemplo seguinte imprime as propriedades de todos os clusters para a subscrição atual:

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

Pode atualizar as etiquetas de um determinado cluster da seguinte forma:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Exemplo

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Redimensionar o cluster

Pode redimensionar o número de um determinado cluster de nós de trabalho ao especificar um novo tamanho da seguinte forma:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorização de cluster

O SDK de gestão do HDInsight também pode ser utilizado para gerir a monitorização nos seus clusters através do Operations Management Suite (OMS).

Da mesma forma para como criou `ClusterClient` para utilizar para operações de gestão, terá de criar um `ExtensionClient` utilizar para monitorização de operações. Depois de concluir a secção autenticação acima, pode criar um `ExtensionClient` da seguinte forma:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O abaixo monitorização exemplos partem do princípio de que já ter inicializado um `ExtensionClient` chamado `extClient` e defina seu `Authorizer` conforme mostrado acima.

### <a name="enable-oms-monitoring"></a>Ativar a monitorização do OMS

> [!NOTE]  
> Para ativar a monitorização do OMS, tem de ter uma área de trabalho do Log Analytics existente. Se ainda não tenha criado uma, pode saber como fazê-lo aqui: [Criar uma área de trabalho do Log Analytics no portal do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Para ativar a monitorização do OMS no seu cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Ver o estado de monitorização do OMS

Para obter o estado do OMS no seu cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Desativar a monitorização do OMS

Para desativar o OMS no seu cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Ações do script

HDInsight fornece uma função de configuração chamada ações de script que invoca scripts personalizados para personalizar o cluster.

> [!NOTE]  
> Obter mais informações sobre como utilizar as ações de script podem ser encontradas aqui: [Personalizar clusters do HDInsight baseado em Linux utilizar ações de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Executar ações de script

Pode executar ações de script num determinado cluster da seguinte forma:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Para as operações de 'Eliminar a ação de Script' e 'Lista de ações de Script persistentes', tem de criar uma `ScriptActionsClient`, da mesma forma para como criou `ClusterClient` a utilizar para operações de gestão. Depois de concluir a secção autenticação acima, pode criar um `ScriptActionsClient` da seguinte forma:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O abaixo das ações de script exemplos partem do princípio que já tenha inicializado um `ScriptActionsClient` chamado `scriptActionsClient` e defina seu `Authorizer` conforme mostrado acima.

### <a name="delete-script-action"></a>Eliminar ação de script

Para eliminar uma ação de script persistentes especificado num determinado cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista de ações de script persistentes

> [!NOTE]  
> Ambos `ListByCluster()` retorna um `ScriptActionsListPage` struct. Para obter a página seguinte, pode chamar `Next()`. Ela pode ser repetida até `ClusterListResultPage.NotDone()` retorna `false`, conforme mostrado no exemplo abaixo.

Para listar todas as ações de script persistentes para o cluster especificado:
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

### <a name="list-all-scripts-execution-history"></a>Lista de histórico de execução de todos os scripts

Para esta operação, tem de criar uma `ScriptExecutionHistoryClient`, da mesma forma para como criou `ClusterClient` a utilizar para operações de gestão. Depois de concluir a secção autenticação acima, pode criar um `ScriptActionsClient` da seguinte forma:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O abaixo pressupõe que já ter inicializado um `ScriptExecutionHistoryClient` chamado `scriptExecutionHistoryClient` e defina seu `Authorizer` conforme mostrado acima.

Para listar o histórico de execução de todos os scripts para o cluster especificado:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Este exemplo imprime todos os detalhes para todas as últimas execuções do script.

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

## <a name="next-steps"></a>Passos Seguintes

* Explore os [material de referência do GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). Os GoDocs fornecem documentação de referência para todas as funções no SDK.
