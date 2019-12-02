---
title: Usar o recurso de banco de dados de acompanhamento para anexar bancos de dados no Azure Data Explorer
description: Saiba mais sobre como anexar bancos de dados no Azure Data Explorer usando o recurso de banco de dados de acompanhamento.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: dd2c29632d70da64251c5e1736a9cb7d82f5d0dc
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667356"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Usar o banco de dados de acompanhamento para anexar bancos de dados no Azure Data Explorer

O recurso de **banco de dados de acompanhamento** permite anexar um banco de dados localizado em um cluster diferente ao cluster de data Explorer do Azure. O **banco de dados de acompanhamento** é anexado no modo *somente leitura* , possibilitando a exibição dos dados e a execução de consultas nos dados que foram ingeridos no banco de dado de **líder**. O banco de dados de acompanhamento sincroniza as mudanças nos bancos de dados de líder. Devido à sincronização, há um atraso de dados de alguns segundos a alguns minutos na disponibilidade de dados. O comprimento do intervalo de tempo depende do tamanho geral dos metadados do banco de dados de líder. Os bancos de dados de líder e de acompanhamento usam a mesma conta de armazenamento para buscar o dado. O armazenamento pertence ao banco de dados líder. O banco de dados de acompanhamento exibe os dados sem a necessidade de ingerir. Uma vez que o banco de dados anexado é um banco de dado somente leitura, não é possível modificar as tabelas, os mesmos e as políticas do banco de dados, exceto a [política de cache](#configure-caching-policy), as [entidades](#manage-principals)e [as permissões](#manage-permissions). Os bancos de dados anexados não podem ser excluídos. Eles devem ser desanexados pelo líder ou do seguidor e, em seguida, podem ser excluídos. 

A anexação de um banco de dados a um cluster diferente usando a funcionalidade de acompanhamento é usada como a infraestrutura de compartilhamento de dados entre organizações e equipes. O recurso é útil para separar os recursos de computação para proteger um ambiente de produção de casos de uso que não sejam de produção. O acompanhamento também pode ser usado para associar o custo do cluster de Data Explorer do Azure à entidade que executa consultas nos dados.

## <a name="which-databases-are-followed"></a>Quais bancos de dados são seguidos?

* Um cluster pode seguir um banco de dados, vários bancos de dados ou todos os bancos de dados de um cluster de líder. 
* Um único cluster pode seguir os bancos de dados de vários clusters de líder. 
* Um cluster pode conter bancos de dados de acompanhamento e bancos de dados de preenchimento

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
1. [Crie cluster e BD](/azure/data-explorer/create-cluster-database-portal) para o líder e o acompanhamento.
1. [Ingerir dados](/azure/data-explorer/ingest-sample-data) para o banco de dado principal usando um dos vários métodos discutidos em [visão geral de ingestão](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Anexar uma base de dados

Há vários métodos que você pode usar para anexar um banco de dados. Neste artigo, abordamos a anexação de um C# banco de dados usando o ou um modelo Azure Resource Manager. Para anexar um banco de dados, você deve ter permissões no cluster de líderes e no cluster de acompanhamento. Para obter mais informações sobre permissões, consulte [Manage Permissions](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Anexar um banco de dados usandoC#

#### <a name="needed-nugets"></a>NuGets necessário

* Instale [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [Microsoft. REST. ClientRuntime. Azure. Authentication para autenticação](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Exemplo de código

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
var attachedDatabaseConfigurationName = "adc";
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

### <a name="attach-a-database-using-python"></a>Anexar um banco de dados usando o Python

#### <a name="needed-modules"></a>Módulos necessários

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Exemplo de código

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
attached_database_Configuration_name = "adc"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Anexar um banco de dados usando um modelo de Azure Resource Manager

Nesta seção, você aprenderá a anexar um banco de dados usando um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
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
                "description": "Name of the leader cluster to create."
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
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
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

Você pode implantar o modelo de Azure Resource Manager [usando o portal do Azure](https://portal.azure.com) ou usando o PowerShell.

   ![implantação de modelo](media/follower/template-deployment.png)


|**Definição**  |**Descrição**  |
|---------|---------|
|Nome do cluster de acompanhamento     |  O nome do cluster de acompanhamento       |
|Nome das configurações do banco de dados anexado    |    O nome do objeto de configurações de banco de dados anexado. O nome deve ser exclusivo no nível do cluster.     |
|Nome da Base de Dados     |      O nome do banco de dados a ser seguido. Se você quiser seguir todos os bancos de dados do líder, use ' * '.   |
|ID de recurso de cluster de líder    |   A ID de recurso do cluster de líder.      |
|Tipo de modificação de entidades de segurança padrão    |   O tipo de modificação principal padrão. Pode ser `Union`, `Replace` ou `None`. Para obter mais informações sobre o tipo de modificação principal padrão, consulte o [comando de controle do tipo de modificação principal](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Localização   |   O local de todos os recursos. O líder e o seguidor devem estar no mesmo local.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Verifique se o banco de dados foi anexado com êxito

Para verificar se o banco de dados foi anexado com êxito, localize seus bancos de dados anexados no [portal do Azure](https://portal.azure.com). 

1. Navegue até o cluster de acompanhamento e selecione **bancos de dados**
1. Pesquise novos bancos de dados somente leitura na lista de banco de dados.

    ![Banco de dados de acompanhamento somente leitura](media/follower/read-only-follower-database.png)

Como alternativa

1. Navegue até o cluster de líderes e selecione **bancos de dados**
2. Verifique se os bancos de dados relevantes estão marcados como **compartilhados com outras pessoas** > **Sim**

    ![Ler e gravar bancos de dados anexados](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Desanexar o banco de dados de acompanhamento usandoC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desanexar o banco de dados de acompanhamento anexado do cluster de acompanhamento

O cluster de acompanhamento pode desanexar qualquer banco de dados anexado da seguinte maneira:

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
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desanexar o banco de dados de acompanhamento anexado do cluster de líderes

O cluster líder pode desanexar qualquer banco de dados anexado da seguinte maneira:

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
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Desanexar o banco de dados de acompanhamento usando Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Desanexar o banco de dados de acompanhamento anexado do cluster de acompanhamento

O cluster de acompanhamento pode desanexar qualquer banco de dados anexado da seguinte maneira:

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
attached_database_configurationName = "adc"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Desanexar o banco de dados de acompanhamento anexado do cluster de líderes

O cluster líder pode desanexar qualquer banco de dados anexado da seguinte maneira:

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
attached_database_configuration_name = "adc"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gerenciar entidades, permissões e política de cache

### <a name="manage-principals"></a>Gerenciar entidades de segurança

Ao anexar um banco de dados, especifique o **"tipo de modificação de entidades de segurança padrão"** . O padrão é manter a coleção de banco de dados de líder de [entidades autorizadas](/azure/kusto/management/access-control/index#authorization)

|**Quase** |**Descrição**  |
|---------|---------|
|**Unida**     |   As entidades de segurança de banco de dados anexadas sempre incluirão as entidades de banco de dados originais, além de novas entidades de segurança adicionais adicionadas ao banco de dados de acompanhamento.      |
|**Substitua**   |    Nenhuma herança de entidades de segurança do banco de dados original. Novas entidades de segurança devem ser criadas para o banco de dados anexado.     |
|**None**   |   As entidades de banco de dados anexadas incluem apenas as entidades do banco de dados original sem entidades adicionais.      |

Para obter mais informações sobre como usar comandos de controle para configurar as entidades de segurança autorizadas, consulte [comandos de controle para gerenciar um cluster de acompanhamento](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Gerenciar permissões

O gerenciamento da permissão de banco de dados somente leitura é o mesmo que para todos os tipos de banco de dados. Consulte [gerenciar permissões no portal do Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Configurar política de cache

O administrador de banco de dados de acompanhamento pode modificar a [política de cache](/azure/kusto/management/cache-policy) do banco de dados anexado ou qualquer uma de suas tabelas no cluster de hospedagem. O padrão é manter a coleção de banco de dados líder de políticas de cache de banco de dados e nível de tabela. Você pode, por exemplo, ter uma política de cache de 30 dias no banco de dados de líder para executar relatórios mensais e uma política de cache de três dias no banco de dados de acompanhamento para consultar somente os dados recentes para solução de problemas. Para obter mais informações sobre como usar comandos de controle para configurar a política de cache no banco de dados ou tabela de acompanhamento, consulte [comandos de controle para gerenciar um cluster de acompanhamento](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Limitações

* O acompanhamento e os clusters de líderes devem estar na mesma região.
* A [ingestão de streaming](/azure/data-explorer/ingest-data-streaming) não pode ser usada em um banco de dados que está sendo seguido.
* Você não pode excluir um banco de dados que está anexado a um cluster diferente antes de desanexá-lo.
* Não é possível excluir um cluster que tenha um banco de dados anexado a um cluster diferente antes de desanexá-lo.
* Não é possível parar um cluster que tenha bancos de dados de acompanhamento ou de líder anexados. 

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a configuração do cluster de acompanhamento, consulte [comandos de controle para gerenciar um cluster de acompanhamento](/azure/kusto/management/cluster-follower).