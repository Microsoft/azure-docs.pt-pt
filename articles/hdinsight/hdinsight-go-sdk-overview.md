---
title: SDK do Azure HDInsight para go
description: Material de referência para usar o SDK do Azure HDInsight para clusters de Go e Apache Hadoop
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/8/2019
ms.author: tyfox
ms.reviewer: jasonh
ms.custom: seodec18
ms.openlocfilehash: 60ac0509aed1fc83bc7f660783d4bdbd6cb7d976
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077123"
---
# <a name="hdinsight-sdk-for-go-preview"></a>SDK do HDInsight para go (visualização)

## <a name="overview"></a>Descrição geral
O SDK do HDInsight para go fornece classes e funções que permitem que você gerencie seus clusters HDInsight. Ele inclui operações para criar, excluir, atualizar, listar, redimensionar, executar ações de script, monitorar, obter propriedades de clusters HDInsight e muito mais.

> [!NOTE]  
>O material de referência do GoDoc para esse SDK também está [disponível aqui](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se você não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* [Vá](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalação do SDK

No local do GOPATH, execute`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Primeiro, o SDK precisa ser autenticado com sua assinatura do Azure.  Siga o exemplo abaixo para criar uma entidade de serviço e usá-la para autenticar. Depois que isso for feito, você terá uma instância de a `ClustersClient`, que contém muitas funções (descritas nas seções abaixo) que podem ser usadas para executar operações de gerenciamento.

> [!NOTE]  
> Há outras maneiras de autenticar além do exemplo abaixo que poderia ser mais adequado às suas necessidades. Todas as funções são descritas aqui: [Funções de autenticação no SDK do Azure para linguagem Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exemplo de autenticação usando uma entidade de serviço

Primeiro, faça logon no [Azure cloud Shell](https://shell.azure.com/bash). Verifique se você está usando atualmente a assinatura na qual deseja que a entidade de serviço seja criada. 

```azurecli-interactive
az account show
```

As informações de sua assinatura são exibidas como JSON.

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

Se você não estiver conectado à assinatura correta, selecione a correta executando: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Se você ainda não registrou o provedor de recursos do HDInsight por outra função (por exemplo, criando um cluster HDInsight por meio do portal do Azure), você precisa fazer isso uma vez para poder autenticar. Isso pode ser feito no [Azure cloud Shell](https://shell.azure.com/bash) executando o seguinte comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Em seguida, escolha um nome para a entidade de serviço e crie-a com o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

As informações da entidade de serviço são exibidas como JSON.

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
Copie o trecho de código abaixo e `TENANT_ID`preencha `CLIENT_ID`, `CLIENT_SECRET`, e `SUBSCRIPTION_ID` com as cadeias de caracteres do JSON que foi retornado após a execução do comando para criar a entidade de serviço.

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
> Esta seção pressupõe que você já tenha autenticado e construído `ClusterClient` uma instância e armazene-a em `client`uma variável chamada. As instruções para autenticação e obtenção `ClusterClient` de um podem ser encontradas na seção de autenticação acima.

### <a name="create-a-cluster"></a>Criar um cluster

Um novo cluster pode ser criado chamando `client.Create()`. 

#### <a name="example"></a>Exemplo

Este exemplo demonstra como criar um cluster [Apache Spark](https://spark.apache.org/) com dois nós de cabeçalho e um nó de trabalho.

> [!NOTE]  
> Primeiro, você precisa criar um grupo de recursos e uma conta de armazenamento, conforme explicado abaixo. Se você já os criou, poderá ignorar essas etapas.

##### <a name="creating-a-resource-group"></a>Criando um grupo de recursos

Você pode criar um grupo de recursos usando o [Azure cloud Shell](https://shell.azure.com/bash) executando
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Criar uma conta de armazenamento

Você pode criar uma conta de armazenamento usando o [Azure cloud Shell](https://shell.azure.com/bash) executando:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Agora, execute o seguinte comando para obter a chave para sua conta de armazenamento (você precisará dela para criar um cluster):
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
O trecho de código go abaixo cria um cluster Spark com dois nós de cabeçalho e um nó de trabalho. Preencha as variáveis em branco conforme explicado nos comentários e sinta-se à vontade para alterar outros parâmetros de acordo com suas necessidades específicas.

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

### <a name="get-cluster-details"></a>Obter detalhes do cluster

Para obter propriedades para um determinado cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Você pode usar `get` o para confirmar que você criou o cluster com êxito.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A saída deve ser semelhante a:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Listar clusters

#### <a name="list-clusters-under-the-subscription"></a>Listar clusters na assinatura
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Listar clusters por grupo de recursos
```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> E retornam uma`ClusterListResultPage`struct. `List()` `ListByResourceGroup()` Para obter a próxima página, você pode chamar `Next()`. Isso pode ser repetido `ClusterListResultPage.NotDone()` até `false`o retorno, conforme mostrado no exemplo abaixo.

#### <a name="example"></a>Exemplo
O exemplo a seguir imprime as propriedades de todos os clusters para a assinatura atual:

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

Para excluir um cluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Atualizar marcas de cluster

Você pode atualizar as marcas de um determinado cluster da seguinte forma:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Exemplo

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Redimensionar cluster

Você pode redimensionar um determinado número de nós de trabalho do cluster especificando um novo tamanho como este:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorização de cluster

O SDK de gerenciamento do HDInsight também pode ser usado para gerenciar o monitoramento em seus clusters por meio do OMS (Operations Management Suite).

Da mesma forma como você `ClusterClient` criou para usar o para operações de gerenciamento, você precisa `ExtensionClient` criar um para usar para operações de monitoramento. Depois de concluir a seção de autenticação acima, você pode criar uma `ExtensionClient` da seguinte maneira:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de monitoramento abaixo pressupõem que você já `ExtensionClient` tenha `extClient` inicializado um `Authorizer` chamado e definido como mostrado acima.

### <a name="enable-oms-monitoring"></a>Habilitar o monitoramento do OMS

> [!NOTE]  
> Para habilitar o monitoramento do OMS, você deve ter um espaço de trabalho Log Analytics existente. Se você ainda não criou um, você pode aprender a fazer isso aqui: [Crie um espaço de trabalho log Analytics no portal do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Para habilitar o monitoramento do OMS em seu cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Exibir status do monitoramento do OMS

Para obter o status do OMS em seu cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Desabilitar o monitoramento do OMS

Para desabilitar o OMS em seu cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Ações do script

O HDInsight fornece uma função de configuração chamada ações de script que invoca scripts personalizados para personalizar o cluster.

> [!NOTE]  
> Mais informações sobre como usar ações de script podem ser encontradas aqui: [Personalizar clusters HDInsight baseados em Linux usando ações de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Executar ações de script

Você pode executar ações de script em um determinado cluster da seguinte forma:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Para as operações ' Excluir ação de script ' e ' listar ações de script persistentes ', você precisa `ScriptActionsClient`criar um, da mesma forma `ClusterClient` como você criou para usar para operações de gerenciamento. Depois de concluir a seção de autenticação acima, você pode criar uma `ScriptActionsClient` desta forma:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Os exemplos de ações de script abaixo pressupõem que você `ScriptActionsClient` já `scriptActionsClient` tenha inicializado `Authorizer` um chamado e definido como mostrado acima.

### <a name="delete-script-action"></a>Excluir ação de script

Para excluir uma ação de script persistente especificada em um determinado cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Listar ações de script persistentes

> [!NOTE]  
> Ambos `ListByCluster()` retornam `ScriptActionsListPage` uma struct. Para obter a próxima página, você pode chamar `Next()`. Isso pode ser repetido `ClusterListResultPage.NotDone()` até `false`o retorno, conforme mostrado no exemplo abaixo.

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

### <a name="list-all-scripts-execution-history"></a>Listar o histórico de execução de todos os scripts

Para essa operação, você precisa criar um `ScriptExecutionHistoryClient`, da mesma forma como você criou `ClusterClient` para usar o para operações de gerenciamento. Depois de concluir a seção de autenticação acima, você pode criar uma `ScriptActionsClient` desta forma:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> O seguinte pressupõe que você já tenha inicializado `scriptExecutionHistoryClient` um `ScriptExecutionHistoryClient` chamado e `Authorizer` definido como mostrado acima.

Para listar o histórico de execução de todos os scripts para o cluster especificado:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exemplo

Este exemplo imprime todos os detalhes de todas as execuções de script anteriores.

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

* Explore o [material de referência do GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). O GoDocs fornece documentação de referência para todas as funções no SDK.
