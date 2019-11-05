---
title: Serviços do Azure que dão suporte a identidades gerenciadas para recursos do Azure
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0731510977c01b08d9aa557246dce3bd92b2f826
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473225"
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
| Atribuído pelo sistema | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | 
| Atribuído pelo usuário | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista a seguir para configurar a identidade gerenciada para máquinas virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais do Azure

|Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| Atribuído pelo usuário | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista a seguir para configurar a identidade gerenciada para conjuntos de dimensionamento de máquinas virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Disponível | Disponível |
| Atribuído pelo usuário | Disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o serviço de Azure App (em regiões onde estiver disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Disponível | Disponível | Não disponível | Não disponível |

Consulte a lista a seguir para usar uma identidade gerenciada com [plantas do Azure](../../governance/blueprints/overview.md):

- [Portal do Azure-atribuição de plano gráfico](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST-atribuição de Blueprint](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerenciada |Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Disponível | Disponível |
| Atribuído pelo usuário | Disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure Functions (em regiões onde estiver disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Pré-visualização | Pré-visualização | Não disponível | Pré-visualização |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para aplicativos lógicos do Azure (em regiões onde disponível):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure Data Factory v2 (em regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o gerenciamento de API do Azure (em regiões onde disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Linux: versão prévia<br>Windows: não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Linux: versão prévia<br>Windows: não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para instâncias de contêiner do Azure (em regiões onde disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Tipo de identidade gerenciada | Todos disponíveis de forma geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para tarefas do registro de contêiner do Azure (em regiões onde disponível):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que dão suporte à autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com os serviços de cliente que usam identidades gerenciadas para recursos do Azure.

### <a name="azure-resource-manager"></a>Gestor de Recursos do Azure

Consulte a lista a seguir para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso via portal do Azure](howto-assign-access-portal.md)
- [Atribuir acesso via PowerShell](howto-assign-access-powershell.md)
- [Atribuir acesso via CLI do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso via modelo de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://management.azure.com/`| Disponível |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://management.microsoftazure.de/` | Disponível |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponível |

### <a name="azure-key-vault"></a>Azure Key Vault

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://vault.azure.net`| Disponível |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponível |
| Azure Alemanha |  `https://vault.microsoftazure.de` | Disponível |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponível |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://datalake.azure.net/` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://database.windows.net/` | Disponível |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://database.cloudapi.de/` | Disponível |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponível |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://eventhubs.azure.net` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-service-bus"></a>Azure Service Bus

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://servicebus.azure.net`  | Disponível |
| Azure Government |  | Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |









### <a name="azure-storage-blobs-and-queues"></a>BLOBs e filas do armazenamento do Azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponível |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Disponível |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Disponível |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Disponível |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Global do Azure | `https://*.asazure.windows.net` | Disponível |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Disponível |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | Disponível |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Disponível |
