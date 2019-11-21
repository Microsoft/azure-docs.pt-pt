---
title: Azure Services that support managed identities - Azure AD
description: List of services that support managed identities for Azure resources and Azure AD authentication
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224293"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services that support managed identities for Azure resources

Managed identities for Azure resources provide Azure services with an automatically managed identity in Azure Active Directory. Using a managed identity, you can authenticate to any service that supports Azure AD authentication without having credentials in your code. We are in the process of integrating managed identities for Azure resources and Azure AD authentication across Azure. Check back often for updates.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

The following Azure services support managed identities for Azure resources:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | 
| User assigned | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Refer to the following list to configure managed identity for Azure Virtual Machines (in regions where available):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| User assigned | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Refer to the following list to configure managed identity for Azure Virtual Machine Scale Sets (in regions where available):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Disponível | Disponível | Disponível |
| User assigned | Disponível | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure App Service (in regions where available):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [O Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Disponível | Não disponível | Não disponível |
| User assigned | Disponível | Disponível | Não disponível | Não disponível |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Managed identity type |All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Disponível | Disponível | Disponível |
| User assigned | Disponível | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure Functions (in regions where available):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [O Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Pré-visualização | Pré-visualização | Não disponível | Pré-visualização |
| User assigned | Não disponível | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure Logic Apps (in regions where available):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Não disponível | Não disponível | Não disponível |
| User assigned | Não disponível | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure Data Factory V2 (in regions where available):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Disponível | Não disponível | Não disponível |
| User assigned | Não disponível | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure API Management (in regions where available):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Linux: Preview<br>Windows: Not available | Não disponível | Não disponível | Não disponível |
| User assigned | Linux: Preview<br>Windows: Not available | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure Container Instances (in regions where available):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Disponível | Não disponível | Não disponível | Não disponível |
| User assigned | Pré-visualização | Não disponível | Não disponível | Não disponível |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services that support Azure AD authentication

The following services support Azure AD authentication, and have been tested with client services that use managed identities for Azure resources.

### <a name="azure-resource-manager"></a>Gestor de Recursos do Azure

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Disponível |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://management.microsoftazure.de/` | Disponível |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponível |

### <a name="azure-key-vault"></a>Azure Key Vault

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Disponível |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponível |
| Azure Alemanha |  `https://vault.microsoftazure.de` | Disponível |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponível |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponível |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://database.cloudapi.de/` | Disponível |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponível |

### <a name="azure-event-hubs"></a>Hubs de Eventos do Azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-service-bus"></a>Azure Service Bus

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Disponível |
| Azure Government |  | Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Disponível |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Disponível |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Disponível |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Disponível |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Disponível |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | Disponível |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Disponível |
