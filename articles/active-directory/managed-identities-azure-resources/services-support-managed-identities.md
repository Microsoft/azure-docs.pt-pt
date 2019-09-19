---
title: Serviços do Azure que dão suporte a identidades gerenciadas para recursos do Azure
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37eba245933bc94e9fb631abd3b4ff495f8c6d67
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087606"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que dão suporte a identidades gerenciadas para recursos do Azure

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD sem ter credenciais em seu código. Estamos no processo de integração de identidades gerenciadas para recursos do Azure e autenticação do Azure AD no Azure. Verifique com frequência se há atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | 
| Utilizador atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista a seguir para configurar a identidade gerenciada para máquinas virtuais do Azure (em regiões onde disponível):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais do Azure

|Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista a seguir para configurar a identidade gerenciada para conjuntos de dimensionamento de máquinas virtuais do Azure (em regiões onde disponível):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Disponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o serviço de Azure App (em regiões onde estiver disponível):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Indisponível | Indisponível |
| Utilizador atribuído | Disponível | Disponível | Indisponível | Indisponível |

Consulte a lista a seguir para usar uma identidade gerenciada com [plantas do Azure](../../governance/blueprints/overview.md):

- [Portal do Azure-atribuição de plano gráfico](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST-atribuição de Blueprint](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerenciada |Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Disponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure Functions (em regiões onde estiver disponível):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Pré-visualização | Pré-visualização | Indisponível | Pré-visualização |
| Utilizador atribuído | Indisponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para aplicativos lógicos do Azure (em regiões onde disponível):

- [Azure portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Indisponível | Indisponível | Indisponível |
| Utilizador atribuído | Indisponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure Data Factory v2 (em regiões onde disponível):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gestão de API do Azure

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Indisponível | Indisponível |
| Utilizador atribuído | Indisponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o gerenciamento de API do Azure (em regiões onde disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Linux: Pré-visualização<br>Windows: Indisponível | Indisponível | Indisponível | Indisponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Windows: Indisponível | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para instâncias de contêiner do Azure (em regiões onde disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Indisponível | Indisponível | Indisponível |
| Utilizador atribuído | Pré-visualização | Indisponível | Indisponível | Indisponível |

Consulte a lista a seguir para configurar a identidade gerenciada para tarefas do registro de contêiner do Azure (em regiões onde disponível):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que dão suporte à autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com os serviços de cliente que usam identidades gerenciadas para recursos do Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a lista a seguir para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso via portal do Azure](howto-assign-access-portal.md)
- [Atribuir acesso via PowerShell](howto-assign-access-powershell.md)
- [Atribuir acesso via CLI do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso via modelo de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://management.azure.com/`| Disponível |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://management.microsoftazure.de/` | Disponível |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponível |

### <a name="azure-key-vault"></a>Azure Key Vault

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://vault.azure.net`| Disponível |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponível |
| Azure Alemanha |  `https://vault.microsoftazure.de` | Disponível |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponível |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://datalake.azure.net/` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-sql"></a>Azure SQL 

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://database.windows.net/` | Disponível |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://database.cloudapi.de/` | Disponível |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponível |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://eventhubs.azure.net` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-service-bus"></a>Azure Service Bus

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://servicebus.azure.net`  | Disponível |
| Azure Government |  | Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-storage-blobs-and-queues"></a>BLOBs e filas do armazenamento do Azure

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID de recurso | State |
|--------|------------|--------|
| Global do Azure | `https://*.asazure.windows.net` | Disponível |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Disponível |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | Disponível |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Disponível |
