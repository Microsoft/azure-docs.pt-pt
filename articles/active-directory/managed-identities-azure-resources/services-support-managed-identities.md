---
title: Serviços Azure que suportam identidades geridas - Azure AD
description: Lista de serviços que suportam identidades geridas para recursos Azure e autenticação da AD Azure
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0b79a27526054f76d9d44e277c401e93214ec3c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018708"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que suportam identidades geridas para recursos Azure

Identidades geridas para os recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure. Utilizando uma identidade gerida, pode autenticar qualquer serviço que suporte a autenticação AD Azure sem ter credenciais no seu código. Estamos em processo de integração de identidades geridas para recursos Azure e autenticação Azure AD em azure. Volte muitas vezes para obter atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços Azure suportam identidades geridas para os recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | 
| Utilizador atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a seguinte lista para configurar a identidade gerida para máquinas virtuais Azure (em regiões onde estiver disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de escala de máquina seleção azul

|Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a seguinte lista para configurar a identidade gerida para conjuntos de escala de máquinas virtuais Azure (em regiões onde estiver disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>App Service do Azure

| Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para o Azure App Service (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [O Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Não disponível | Não disponível |
| Utilizador atribuído | Disponível | Disponível | Não disponível | Não disponível |

Consulte a seguinte lista para utilizar uma identidade gerida com [plantas Azure:](../../governance/blueprints/overview.md)

- [Portal Azure - atribuição de plantas](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - atribuição de plantas](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerido |Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para funções azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [O Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Pré-visualização | Pré-visualização | Não disponível | Pré-visualização |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as Aplicações Lógicas Azure (em regiões onde disponível):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Azure Data Factory V2 (em regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Gestão da API Azure (em regiões onde disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Linux: Pré-visualização<br>Janelas: Não disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Janelas: Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as instâncias de contentores azure (em regiões onde estiver disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as tarefas de registo de contentores azure (em regiões onde estiver disponível):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Identidade Gerida para Aplicações](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) de Tecido de Serviço está em Pré-visualização e disponível em todas as regiões.

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Não Disponível | Não Disponível | não disponível |
| Utilizador atribuído | Disponível | Não Disponível | Não Disponível |Não Disponível |

Consulte a seguinte lista para configurar a identidade gerida para aplicações azure service fabric em todas as regiões:
- [Modelo do Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços Azure que suportam autenticação DaAzure

Os seguintes serviços suportam a autenticação da Azure AD, tendo sido testados com serviços de clientes que utilizam identidades geridas para recursos Azure.

### <a name="azure-resource-manager"></a>Gestor de Recursos do Azure

Consulte a seguinte lista para configurar o acesso ao Gestor de Recursos do Azure:

- [Atribuir acesso via portal Azure](howto-assign-access-portal.md)
- [Atribuir acesso via Powershell](howto-assign-access-powershell.md)
- [Atribua acesso via Azure CLI](howto-assign-access-CLI.md)
- [Atribuir acesso através do modelo de Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md)

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

### <a name="azure-event-hubs"></a>Hubs de Eventos do Azure

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









### <a name="azure-storage-blobs-and-queues"></a>Blobs e filas de armazenamento azure

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
