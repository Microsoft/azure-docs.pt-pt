---
title: Serviços Azure que suportam identidades geridas - Azure AD
description: Lista de serviços que suportam identidades geridas para recursos Azure e autenticação da AD Azure
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45a248300cd61a0e57b9f35c8905d7c335069a27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991284"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que suportam identidades geridas para recursos Azure

Identidades geridas para os recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure. Utilizando uma identidade gerida, pode autenticar qualquer serviço que suporte a autenticação AD Azure sem ter credenciais no seu código. Estamos em processo de integração de identidades geridas para recursos Azure e autenticação Azure AD em azure. Volte muitas vezes para obter atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços Azure suportam identidades geridas para os recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualização | Pré-visualização | 
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualização | Pré-visualização |

Consulte a seguinte lista para configurar a identidade gerida para máquinas virtuais Azure (em regiões onde estiver disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais do Azure

|Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | ![Disponível][check] | Pré-visualização | Pré-visualização | Pré-visualização |

Consulte a seguinte lista para configurar a identidade gerida para conjuntos de escala de máquinas virtuais Azure (em regiões onde estiver disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check] |

Consulte a seguinte lista para configurar a identidade gerida para o Azure App Service (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo de Gestor de Recursos Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |

Consulte a seguinte lista para utilizar uma identidade gerida com [plantas Azure:](../../governance/blueprints/overview.md)

- [Portal Azure - atribuição de plantas](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - atribuição de plantas](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerido |Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check]  |

Consulte a seguinte lista para configurar a identidade gerida para funções azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo de Gestor de Recursos Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |


Consulte a seguinte lista para configurar a identidade gerida para as Aplicações Lógicas Azure (em regiões onde disponível):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Azure Data Factory V2 (em regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Pré-visualização | Pré-visualização | Não disponível | Pré-visualização |

Consulte a seguinte lista para configurar a identidade gerida para a Gestão da API Azure (em regiões onde disponível):

- [Modelo de Gestor de Recursos Azure](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Linux: Pré-visualização<br>Janelas: Não disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Janelas: Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as instâncias de contentores azure (em regiões onde estiver disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo de Gestor de Recursos Azure](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as tarefas de registo de contentores azure (em regiões onde estiver disponível):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Identidade Gerida para Aplicações](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) de Tecido de Serviço está em Pré-visualização e disponível em todas as regiões.

Tipo de identidade gerido | Todos geralmente disponíveis<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Não Disponível | Não Disponível | não disponível |
| Utilizador atribuído | ![Disponível][check] | Não Disponível | Não Disponível |Não Disponível |

Consulte a seguinte lista para configurar a identidade gerida para aplicações azure service fabric em todas as regiões:
- [Modelo de Gestor de Recursos Azure](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços Azure que suportam autenticação DaAzure

Os seguintes serviços suportam a autenticação da Azure AD, tendo sido testados com serviços de clientes que utilizam identidades geridas para recursos Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a seguinte lista para configurar o acesso ao Gestor de Recursos do Azure:

- [Atribuir acesso via portal Azure](howto-assign-access-portal.md)
- [Atribuir acesso via PowerShell](howto-assign-access-powershell.md)
- [Atribua acesso via Azure CLI](howto-assign-access-CLI.md)
- [Atribuir acesso através do modelo de Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponível][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://management.microsoftazure.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponível][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha |  `https://vault.microsoftazure.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Disponível][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponível][check] |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-sql"></a>SQL do Azure 

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponível][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://database.cloudapi.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Disponível][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponível][check] |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-service-bus"></a>Service Bus do Azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponível][check] |
| Azure Government |  | ![Disponível][check] |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |









### <a name="azure-storage-blobs-and-queues"></a>Blobs e filas de armazenamento azure

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponível][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponível][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponível][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Disponível][check] |

> [!Note]
> O Microsoft Power BI também [suporta identidades geridas.](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)


[check]: media/services-support-managed-identities/check.png "Disponível"
