---
title: Serviços do Azure que o suporte de identidades geridas para recursos do Azure
description: Lista de serviços que oferecem suporte a identidades geridas para recursos do Azure e a autenticação do Azure AD
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f6c511c605a98a696fb542638e86e63f0226150
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340259"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que suportam identidades geridas para recursos do Azure

Identidades geridas para recursos do Azure fornecem serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Utilizar uma identidade gerida, pode autenticar a qualquer serviço que suporta a autenticação do Azure AD sem ter credenciais em seu código. Estamos no processo de integração de identidades geridas para recursos do Azure e a autenticação do Azure AD no Azure. Verificar back, muitas vezes, a existência de atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços do Azure suportam identidades geridas para recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | 
| Utilizador atribuído | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista seguinte para configurar a identidade gerida para máquinas de virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de Máquina Virtual do Azure

|Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a lista seguinte para configurar a identidade gerida para conjuntos de dimensionamento de máquinas virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida para o serviço de aplicações do Azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para utilizar uma identidade gerida com [esquemas de Azure](../../governance/blueprints/overview.md):

- [Portal do Azure – atribuição do esquema](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST - atribuição do esquema](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerido |Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida para as funções do Azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Pré-visualização | Pré-visualização | Não disponível | Pré-visualização |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida para o Azure Logic Apps (nas regiões onde disponível):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida para o Azure Data Factory V2 (nas regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida para a gestão de API do Azure (em regiões onde disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerido | Disponíveis em geral<br>Regiões globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista seguinte para configurar a identidade gerida do Azure Container Instances (nas regiões onde disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Suporte do Azure AD que a autenticação de serviços do Azure

Os seguintes serviços de suportam de autenticação do Azure AD e foram testados com serviços de cliente que utilizam identidades geridas para recursos do Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a lista seguinte para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso através do portal do Azure](howto-assign-access-portal.md)
- [Atribuir acesso através do Powershell](howto-assign-access-powershell.md)
- [Atribuir acesso através da CLI do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso por meio do modelo do Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

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

## <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponível |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

## <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponível |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponível |
| Azure Alemanha | `https://database.cloudapi.de/` | Disponível |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponível |

## <a name="azure-event-hubs"></a>Azure Event Hubs

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Pré-visualização |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

## <a name="azure-service-bus"></a>Service Bus do Azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Pré-visualização |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

## <a name="azure-storage"></a>Storage do Azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | Pré-visualização |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |
