---
title: VNet endpoints and rules for single and pooled databases
description: Mark a subnet as a Virtual Network service endpoint. Then the endpoint as a virtual network rule to the ACL your Azure SQL Database. You SQL Database then accepts communication from all virtual machines and other nodes on the subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 4d3c74db9a0c4e13ee7c17eb78552d8c11cd7afb
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422519"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Use virtual network service endpoints and rules for database servers

*Virtual network rules* are one firewall security feature that controls whether the database server for your single databases and elastic pool in Azure [SQL Database](sql-database-technical-overview.md) or for your databases in [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepts communications that are sent from particular subnets in virtual networks. This article explains why the virtual network rule feature is sometimes your best option for securely allowing communication to your Azure SQL Database and SQL Data Warehouse.

> [!IMPORTANT]
> This article applies to Azure SQL server, and to both SQL Database and SQL Data Warehouse databases that are created on the Azure SQL server. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. This article does *not* apply to a **managed instance** deployment in Azure SQL Database because it does not have a service endpoint associated with it.

To create a virtual network rule, there must first be a [virtual network service endpoint][vm-virtual-network-service-endpoints-overview-649d] for the rule to reference.

## <a name="how-to-create-a-virtual-network-rule"></a>How to create a virtual network rule

If you only create a virtual network rule, you can skip ahead to the steps and explanation [later in this article](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Details about virtual network rules

This section describes several details about virtual network rules.

### <a name="only-one-geographic-region"></a>Only one geographic region

Each Virtual Network service endpoint applies to only one Azure region. The endpoint does not enable other regions to accept communication from the subnet.

Any virtual network rule is limited to the region that its underlying endpoint applies to.

### <a name="server-level-not-database-level"></a>Server-level, not database-level

Each virtual network rule applies to your whole Azure SQL Database server, not just to one particular database on the server. In other words, virtual network rule applies at the server-level, not at the database-level.

- In contrast, IP rules can apply at either level.

### <a name="security-administration-roles"></a>Security administration roles

There is a separation of security roles in the administration of Virtual Network service endpoints. Action is required from each of the following roles:

- **Network Admin:** &nbsp; Turn on the endpoint.
- **Database Admin:** &nbsp; Update the access control list (ACL) to add the given subnet to the SQL Database server.

*RBAC alternative:*

The roles of Network Admin and Database Admin have more capabilities than are needed to manage virtual network rules. Only a subset of their capabilities is needed.

You have the option of using [role-based access control (RBAC)][rbac-what-is-813s] in Azure to create a single custom role that has only the necessary subset of capabilities. The custom role could be used instead of involving either the Network Admin or the Database Admin. The surface area of your security exposure is lower if you add a user to a custom role, versus adding the user to the other two major administrator roles.

> [!NOTE]
> In some cases the Azure SQL Database and the VNet-subnet are in different subscriptions. In these cases you must ensure the following configurations:
> - Both subscriptions must be in the same Azure Active Directory tenant.
> - The user has the required permissions to initiate operations, such as enabling service endpoints and adding a VNet-subnet to the given Server.
> - Both subscriptions must have the Microsoft.Sql provider registered.

## <a name="limitations"></a>Limitações

For Azure SQL Database, the virtual network rules feature has the following limitations:

- In the firewall for your SQL Database, each virtual network rule references a subnet. All these referenced subnets must be hosted in the same geographic region that hosts the SQL Database.

- Each Azure SQL Database server can have up to 128 ACL entries for any given virtual network.

- Virtual network rules apply only to Azure Resource Manager virtual networks; and not to [classic deployment model][arm-deployment-model-568f] networks.

- Turning ON virtual network service endpoints to Azure SQL Database also enables the endpoints for the MySQL and PostgreSQL Azure services. However, with endpoints ON, attempts to connect from the endpoints to your MySQL or PostgreSQL instances may fail.
  - The underlying reason is that MySQL and PostgreSQL likely do not have a virtual network rule configured. You must configure a virtual network rule for Azure Database for MySQL and PostgreSQL and the connection will succeed.

- On the firewall, IP address ranges do apply to the following networking items, but virtual network rules do not:
  - [Site-to-Site (S2S) virtual private network (VPN)][vpn-gateway-indexmd-608y]
  - On-premises via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerations when using Service Endpoints

When using service endpoints for Azure SQL Database, review the following considerations:

- **Outbound to Azure SQL Database Public IPs is required**: Network Security Groups (NSGs) must be opened to Azure SQL Database IPs to allow connectivity. You can do this by using NSG [Service Tags](../virtual-network/security-overview.md#service-tags) for Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

If you are using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) from your premises, for public peering or Microsoft peering, you will need to identify the NAT IP addresses that are used. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para peering da Microsoft, o(s) endereço(s) IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo fornecedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
To allow communication from your circuit to Azure SQL Database, you must create IP network rules for the public IP addresses of your NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impact of using VNet Service Endpoints with Azure storage

Azure Storage has implemented the same feature that allows you to limit connectivity to your Azure Storage account. If you choose to use this feature with an Azure Storage account that is being used by Azure SQL Server, you can run into issues. Next is a list and discussion of Azure SQL Database and Azure SQL Data Warehouse features that are impacted by this.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

PolyBase is commonly used to load data into Azure SQL Data Warehouse from Azure Storage accounts. If the Azure Storage account that you are loading data from limits access only to a set of VNet-subnets, connectivity from PolyBase to the Account will break. For enabling both PolyBase import and export scenarios with Azure SQL Data Warehouse connecting to Azure Storage that's secured to VNet, follow the steps indicated below:

#### <a name="prerequisites"></a>Pré-requisitos

- Install Azure PowerShell using this [guide](https://docs.microsoft.com/powershell/azure/install-az-ps).
- If you have a general-purpose v1 or blob storage account, you must first upgrade to general-purpose v2 using this [guide](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
- You must have **Allow trusted Microsoft services to access this storage account** turned on under Azure Storage account **Firewalls and Virtual networks** settings menu. Refer to this [guide](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) for more information.

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passos

1. In PowerShell, **register your Azure SQL Server** hosting your Azure SQL Data Warehouse instance with Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Create a **general-purpose v2 Storage Account** using this [guide](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - If you have a general-purpose v1 or blob storage account, you must **first upgrade to v2** using this [guide](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - For known issues with Azure Data Lake Storage Gen2, please refer to this [guide](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Under your storage account, navigate to **Access Control (IAM)** , and click **Add role assignment**. Assign **Storage Blob Data Contributor** RBAC role to your Azure SQL Server hosting your Azure SQL Data Warehouse which you've registered with Azure Active Directory (AAD) as in step#1.

   > [!NOTE]
   > Only members with Owner privilege can perform this step. For various built-in roles for Azure resources, refer to this [guide](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Polybase connectivity to the Azure Storage account:**

   1. Create a database **[master key](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** if you haven't created one earlier:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Create database scoped credential with **IDENTITY = 'Managed Service Identity'** :

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - There is no need to specify SECRET with Azure Storage access key because this mechanism uses [Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) under the covers.
       > - IDENTITY name should be **'Managed Service Identity'** for PolyBase connectivity to work with Azure Storage account secured to VNet.

   1. Create external data source with abfss:// scheme for connecting to your general-purpose v2 storage account using PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - If you already have external tables associated with general-purpose v1 or blob storage account, you should first drop those external tables and then drop corresponding external data source. Then create external data source with abfss:// scheme connecting to general-purpose v2 storage account as above and re-create all the external tables using this new external data source. You could use [Generate and Publish Scripts Wizard](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) to generate create-scripts for all the external tables for ease.
       > - For more information on abfss:// scheme, refer to this [guide](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - For more information on CREATE EXTERNAL DATA SOURCE, refer to this [guide](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Query as normal using [external tables](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database Blob Auditing

Blob auditing pushes audit logs to your own storage account. If this storage account uses the VNet Service endpoints feature then connectivity from Azure SQL Database to the storage account will break.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adding a VNet Firewall rule to your server without turning On VNet Service Endpoints

Long ago, before this feature was enhanced, you were required to turn VNet service endpoints On before you could implement a live VNet rule in the Firewall. The endpoints related a given VNet-subnet to an Azure SQL Database. But now as of January 2018, you can circumvent this requirement by setting the **IgnoreMissingVNetServiceEndpoint** flag.

Merely setting a Firewall rule does not help secure the server. You must also turn VNet service endpoints On for the security to take effect. When you turn service endpoints On, your VNet-subnet experiences downtime until it completes the transition from Off to On. This is especially true in the context of large VNets. You can use the **IgnoreMissingVNetServiceEndpoint** flag to reduce or eliminate the downtime during transition.

You can set the **IgnoreMissingVNetServiceEndpoint** flag by using PowerShell. For details, see [PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Errors 40914 and 40615

Connection error 40914 relates to *virtual network rules*, as specified on the Firewall pane in the Azure portal. Error 40615 is similar, except it relates to *IP address rules* on the Firewall.

### <a name="error-40914"></a>Error 40914

*Message text:* Cannot open server ' *[server-name]* ' requested by the login. Client is not allowed to access the server.

*Error description:* The client is in a subnet that has virtual network server endpoints. No entanto, o servidor da Base de Dados SQL do Azure não tem nenhuma regra de rede virtual que concede à sub-rede o direito de comunicar com a Base de Dados SQL.

*Error resolution:* On the Firewall pane of the Azure portal, use the virtual network rules control to [add a virtual network rule](#anchor-how-to-by-using-firewall-portal-59j) for the subnet.

### <a name="error-40615"></a>Error 40615

*Message text:* Cannot open server '{0}' requested by the login. Client with IP address '{1}' is not allowed to access the server.

*Error description:* The client is trying to connect from an IP address that is not authorized to connect to the Azure SQL Database server. O servidor da firewall não dispõe de qualquer regra de endereço IP que permita a um cliente comunicar entre esse mesmo endereço IP e a Base de Dados SQL.

*Error resolution:* Enter the client's IP address as an IP rule. Faça isso através do painel Firewall no portal do Azure.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal can create a virtual network rule

This section illustrates how you can use the [Azure portal][http-azure-portal-link-ref-477t] to create a *virtual network rule* in your Azure SQL Database. The rule tells your SQL Database to accept communication from a particular subnet that has been tagged as being a *Virtual Network service endpoint*.

> [!NOTE]
> If you intend to add a service endpoint to the VNet firewall rules of your Azure SQL Database server, first ensure that service endpoints are turned On for the subnet.
>
> If service endpoints are not turned on for the subnet, the portal asks you to enable them. Click the **Enable** button on the same blade on which you add the rule.

## <a name="powershell-alternative"></a>PowerShell alternative

A script can also create virtual network rules using PowerShell cmdlet **New-AzSqlServerVirtualNetworkRule** or [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). If interested, see [PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternative

Internally, the PowerShell cmdlets for SQL VNet actions call REST APIs. You can call the REST APIs directly.

- [Virtual Network Rules: Operations][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

You must already have a subnet that is tagged with the particular Virtual Network service endpoint *type name* relevant to Azure SQL Database.

- The relevant endpoint type name is **Microsoft.Sql**.
- If your subnet might not be tagged with the type name, see [Verify your subnet is an endpoint][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure portal steps

1. Inicie sessão no [portal do Azure][http-azure-portal-link-ref-477t].

2. Then navigate the portal to **SQL servers** &gt; **Firewall / Virtual Networks**.

3. Set the **Allow access to Azure services** control to OFF.

    > [!IMPORTANT]
    > If you leave the control set to ON, your Azure SQL Database server accepts communication from any subnet inside the Azure boundary i.e. originating from one of the IP addresses that is recognized as those within ranges defined for Azure data centers. Leaving the control set to ON might be excessive access from a security point of view. The Microsoft Azure Virtual Network service endpoint feature, in coordination with the virtual network rule feature of SQL Database, together can reduce your security surface area.

4. Click the **+ Add existing** control, in the **Virtual networks** section.

    ![Click add existing (subnet endpoint, as a SQL rule).][image-portal-firewall-vnet-add-existing-10-png]

5. In the new **Create/Update** pane, fill in the controls with the names of your Azure resources.

    > [!TIP]
    > You must include the correct **Address prefix** for your subnet. You can find the value in the portal.
    > Navigate **All resources** &gt; **All types** &gt; **Virtual networks**. The filter displays your virtual networks. Click your virtual network, and then click **Subnets**. The **ADDRESS RANGE** column has the Address prefix you need.

    ![Fill in fields for new rule.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Click the **OK** button near the bottom of the pane.

7. See the resulting virtual network rule on the firewall pane.

    ![See the new rule, on the firewall pane.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> The following statuses or states apply to the rules:
> - **Ready:** Indicates that the operation that you initiated has Succeeded.
> - **Failed:** Indicates that the operation that you initiated has Failed.
> - **Deleted:** Only applies to the Delete operation, and indicates that the rule has been deleted and no longer applies.
> - **InProgress:** Indicates that the operation is in progress. The old rule applies while the operation is in this state.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Azure virtual network service endpoints][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database server-level and database-level firewall rules][sql-db-firewall-rules-config-715d]

The virtual network rule feature for Azure SQL Database became available in late September 2017.

## <a name="next-steps"></a>Passos seguintes

- [Use PowerShell to create a virtual network service endpoint, and then a virtual network rule for Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network Rules: Operations][rest-api-virtual-network-rules-operations-862r] with REST APIs

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
