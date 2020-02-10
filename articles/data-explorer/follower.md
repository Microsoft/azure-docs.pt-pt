---
title: Utilize a função de base de dados do seguidor para anexar bases de dados no Azure Data Explorer
description: Saiba como anexar bases de dados no Azure Data Explorer utilizando a funcionalidade de base de dados do seguidor.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 20b667ae345e468bcd3db25d85b7c9de561af4bc
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111474"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Utilize base de dados de seguidores para anexar bases de dados no Azure Data Explorer

A função de base de **dados do seguidor** permite-lhe anexar uma base de dados localizada num cluster diferente do seu cluster Azure Data Explorer. A base de dados dos **seguidores** está anexada no modo de *leitura apenas,* possibilitando visualizar os dados e executar consultas nos dados que foram ingeridos na base de dados do **líder**. A base de dados do seguidor sincroniza as alterações nas bases de dados dos líderes. Devido à sincronização, há um desfasamento de alguns segundos a alguns minutos na disponibilidade de dados. A duração do tempo de atraso depende do tamanho total dos metadados da base de dados do líder. As bases de dados do líder e dos seguidores usam a mesma conta de armazenamento para recolher os dados. O armazenamento é propriedade da base de dados dos líderes. A base de dados do seguidor vê os dados sem necessidade de ingerir. Uma vez que a base de dados anexa é uma base de dados apenas para leitura, os dados, tabelas e políticas na base de dados não podem ser modificados, exceto para a política de [cache](#configure-caching-policy), [principais](#manage-principals), e [permissões](#manage-permissions). As bases de dados anexas não podem ser eliminadas. Devem ser separadas pelo líder ou pelo seguidor e só assim podem ser eliminadas. 

Anexar uma base de dados a um cluster diferente utilizando a capacidade de seguidoré usado como infraestrutura para partilhar dados entre organizações e equipas. A funcionalidade é útil para segregar recursos computacionais para proteger um ambiente de produção de casos de uso não produtivo. O seguidor também pode ser usado para associar o custo do cluster Do Azure Data Explorer à parte que executa consultas nos dados.

## <a name="which-databases-are-followed"></a>Que bases de dados são seguidas?

* Um cluster pode seguir uma base de dados, várias bases de dados, ou todas as bases de dados de um cluster líder. 
* Um único cluster pode seguir bases de dados de vários grupos de líderes. 
* Um cluster pode conter bases de dados de seguidores e bases de dados de líderes

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
1. [Crie cluster e DB](/azure/data-explorer/create-cluster-database-portal) para o líder e seguidor.
1. [Ingerir dados](/azure/data-explorer/ingest-sample-data) para a base de dados líder utilizando um dos vários métodos discutidos na visão geral da [ingestão](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Anexar uma base de dados

Existem vários métodos que pode utilizar para anexar uma base de dados. Neste artigo, discutimos a anexação de uma base de dados utilizando C# ou um modelo de Gestor de Recursos Azure. Para anexar uma base de dados, deve ter permissões no cluster de líderes e no agrupamento de seguidores. Para obter mais informações sobre permissões, consulte [permissões de gestão](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Anexar uma base de dados utilizandoC#

#### <a name="needed-nugets"></a>Nugets necessários

* Instale [microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [microsoft.rest.clientRuntime.Azure.Autenticação para autenticação](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Exemplo de Código

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Anexar uma base de dados usando Python

#### <a name="needed-modules"></a>Módulos Necessários

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Exemplo de Código

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Fixe uma base de dados utilizando um modelo de Gestor de Recursos Azure

Nesta secção, aprende-se a anexar uma base de dados a um cluser existente utilizando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('attachedDatabaseConfigurationsName')]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Implementar o modelo 

Pode implantar o modelo do Gestor de Recursos Azure [utilizando o portal Azure](https://portal.azure.com) ou utilizando a powershell.

   ![implantação do modelo](media/follower/template-deployment.png)


|**Definição**  |**Descrição**  |
|---------|---------|
|Nome do cluster do seguidor     |  O nome do cluster de acompanhamento. Este é o cluster em que este modelo será implantado.  |
|Nome de configurações de bases de dados anexadas    |    O nome do objeto de configurações de bases de dados anexado. O nome pode ser qualquer corda desde que seja única no nível do cluster.     |
|Nome da Base de Dados     |      O nome da base de dados a seguir. Se quiser seguir todas as bases de dados do líder, use '*'.   |
|ID de recurso de cluster líder    |   A identificação de recursos do cluster líder.      |
|Tipo de modificação de principais predefinidos    |   O tipo de modificação do principal padrão. Pode ser `Union`, `Replace` ou `None`. Para obter mais informações sobre o tipo de modificação principal predefinido, consulte o comando de controlo do tipo de [modificação principal](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Localização   |   A localização de todos os recursos. O líder e o seguidor devem estar no mesmo local.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Verifique se a base de dados foi ligada com sucesso

Para verificar se a base de dados foi ligada com sucesso, encontre as bases de dados anexadas no [portal Azure](https://portal.azure.com). 

1. Navegue para o cluster de seguidores e selecione **Bases de Dados**
1. Procure novas bases de dados só para ler na lista de dados.

    ![Base de dados de seguidores de leitura](media/follower/read-only-follower-database.png)

Alternativamente:

1. Navegue para o cluster líder e selecione **Bases de Dados**
2. Verifique se as bases de dados relevantes estão marcadas como **partilhadas com outros** > **Sim**

    ![Ler e escrever bases de dados anexas](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Desmontar a base de dados do seguidor utilizandoC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desmontar a base de dados de seguidores anexado do cluster de seguidores

O cluster de seguidores pode desprender qualquer base de dados anexada da seguinte forma:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desmontar a base de dados de seguidores anexado do cluster líder

O cluster líder pode separar qualquer base de dados anexada da seguinte forma:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Desmontar a base de dados de seguidores usando python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desmontar a base de dados de seguidores anexado do cluster de seguidores

O cluster de seguidores pode desprender qualquer base de dados anexada da seguinte forma:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desmontar a base de dados de seguidores anexado do cluster líder

O cluster líder pode separar qualquer base de dados anexada da seguinte forma:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gerir princípios, permissões e política de cache

### <a name="manage-principals"></a>Gerir os principais

Ao anexar uma base de dados, especifique o tipo de modificação dos **"principais predefinidos".** O padrão é manter a coleção de bases de dados líder de [diretores autorizados](/azure/kusto/management/access-control/index#authorization)

|**Tipo** |**Descrição**  |
|---------|---------|
|**União**     |   Os diretores de base de dados anexados incluirão sempre os diretores originais da base de dados, além de novos diretores adicionais adicionados à base de dados dos seguidores.      |
|**Substituir**   |    Nenhuma herança de diretores da base de dados original. Devem ser criados novos diretores para a base de dados anexada.     |
|**Nenhuma.**   |   Os diretores de base de dados anexados incluem apenas os diretores da base de dados original sem diretores adicionais.      |

Para obter mais informações sobre a utilização de comandos de controlo para configurar os diretores autorizados, consulte [comandos de controlo para gerir um cluster](/azure/kusto/management/cluster-follower)de seguidores .

### <a name="manage-permissions"></a>Gerir permissões

Gerir a permissão de base de dados apenas para leitura é o mesmo que para todos os tipos de bases de dados. Consulte [permissões de gestão no portal Azure.](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

### <a name="configure-caching-policy"></a>Política de cache configurar

O administrador de base de dados do seguidor pode modificar a política de [cache](/azure/kusto/management/cache-policy) da base de dados anexada ou de qualquer uma das suas tabelas no cluster de hospedagem. O padrão é manter a base de dados dos líderes na recolha de bases de dados e políticas de cache ao nível da mesa. Pode, por exemplo, ter uma política de 30 dias na base de dados dos líderes para executar relatórios mensais e uma política de três dias na base de dados dos seguidores para consultar apenas os dados recentes para resolução de problemas. Para obter mais informações sobre a utilização de comandos de controlo para configurar a política de cache na base de dados ou tabela do seguidor, consulte comandos de [controlo para gerir um cluster de seguidores](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Limitações

* O seguidor e os agrupamentos de líderes devem estar na mesma região.
* [A ingestão](/azure/data-explorer/ingest-data-streaming) de streaming não pode ser usada numa base de dados que está a ser seguida.
* A encriptação de dados utilizando [chaves geridas](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) pelo cliente não é suportada tanto em clusters líderes como em seguidores. 
* Não é possível eliminar uma base de dados que esteja ligada a um cluster diferente antes de a desmontar.
* Não é possível eliminar um cluster que tenha uma base de dados anexada a um cluster diferente antes de o desmontar.
* Não é possível parar um cluster que tenha anexado o acompanhamento ou a base de dados de líderes. 

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a configuração do cluster do seguidor, consulte [comandos de controlo para gerir um cluster de seguidores](/azure/kusto/management/cluster-follower).