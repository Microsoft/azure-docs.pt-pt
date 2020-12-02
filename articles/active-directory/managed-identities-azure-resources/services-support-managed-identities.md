---
title: Serviços Azure que suportam identidades geridas - Azure AD
description: Lista de serviços que suportam identidades geridas para recursos Azure e autenticação AD AZure
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 10/07/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c77f0e93e9f0084f335df39d2878871d66af0aac
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498035"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que suportam identidades geridas para recursos da Azure

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Utilizando uma identidade gerida, pode autenticar qualquer serviço que suporte a autenticação AZure AD sem ter credenciais no seu código. Estamos em processo de integração de identidades geridas para recursos Azure e autenticação AD AZure em Azure. Volte muitas vezes para obter atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços da Azure apoiam identidades geridas para os recursos da Azure:


### <a name="azure-api-management"></a>Gestão de API do Azure

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Pré-visualizar | Pré-visualizar | Não disponível | Pré-visualizar |

Consulte a seguinte lista para configurar a identidade gerida para a Gestão API da Azure (nas regiões onde disponível):

- [Modelo Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Configuração da Aplicação Azure

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não Disponível | Não Disponível |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check]  | Não Disponível  | Não Disponível |

Consulte a seguinte lista para configurar a identidade gerida para a configuração da aplicação Azure (nas regiões onde disponível):

- [CLI do Azure](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check] |

Consulte a seguinte lista para configurar a identidade gerida para o Serviço de Aplicações Azure (nas regiões onde disponível):

- [Portal do Azure](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [CLI do Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modelo Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes ativado pelo Azure Arc

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Pré-visualizar | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Azure Arc habilitado kubernetes atualmente [suporta a identidade atribuída ao sistema](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). O certificado de identidade de serviço gerido é usado por todos os agentes Azure Arc habilitados kubernetes para comunicação com a Azure.

### <a name="azure-automanage"></a>Azure Automanage

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Pré-visualizar | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte o seguinte documento para reconfigurar uma identidade gerida se tiver mudado a sua subscrição para um novo inquilino:
* [Reparar uma conta de auto-nomeagem quebrada](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |

Consulte a seguinte lista para utilizar uma identidade gerida com [plantas Azure:](../../governance/blueprints/overview.md)

- [Portal Azure - atribuição de plantas](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - atribuição de projeto](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |


### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para instâncias de contentores Azure (nas regiões onde disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualizar | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para as tarefas de registo de contentores do Azure (nas regiões onde disponível):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Azure Data Factory V2 (nas regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Azure Event Grid

Tipo de identidade gerido |Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Pré-visualizar | Pré-visualizar | Não disponível | Pré-visualizar |
| Utilizador atribuído | Não disponível | Não disponível  | Não disponível  | Não disponível |









### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerido |Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check]  |

Consulte a seguinte lista para configurar a identidade gerida para as funções Azure (nas regiões onde disponível):

- [Portal do Azure](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [CLI do Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modelo Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Azure Data Factory V2 (nas regiões onde disponível):

- [Portal do Azure](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Importar/Exportar do Microsoft Azure

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível na região onde o serviço de exportação de importações Azure está disponível | Pré-visualizar | Disponível | Disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-kubernetes-service-aks"></a>Serviço de Kubernetes do Azure (AKS)

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualizar | Não disponível | Não disponível | Não disponível |


Para obter mais informações, consulte [utilização de identidades geridas no Serviço Azure Kubernetes](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |


Consulte a seguinte lista para configurar a identidade gerida para Azure Logic Apps (nas regiões onde disponível):

- [Portal do Azure](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Modelo Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Pré-visualizar | Não Disponível | Não disponível | Não disponível |
| Utilizador atribuído | Pré-visualizar | Não disponível | Não disponível | Não disponível |

Para obter mais informações, consulte [utilizar identidades geridas com Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a seguinte lista para configurar a identidade gerida para a Política Azure (nas regiões onde disponível):

- [Portal do Azure](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [CLI do Azure](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- [Modelos de gestor de recursos Azure](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Identidade gerida para aplicações de tecido de serviço](../../service-fabric/concepts-managed-identity.md) está disponível em todas as regiões.

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Não Disponível | Não Disponível | não disponível |
| Utilizador atribuído | ![Disponível][check] | Não Disponível | Não Disponível |Não Disponível |

Consulte a seguinte lista para configurar a identidade gerida para aplicações Azure Service Fabric em todas as regiões:

- [Modelo Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | Não Disponível | Não Disponível | Não Disponível |
| Utilizador atribuído | Não Disponível | Não Disponível | Não Disponível | Não Disponível |


Para obter mais informações, consulte [Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível na região onde o serviço Azure Stack Edge está disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais do Azure

|Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualizar | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualizar | ![Disponível][check] |

Consulte a seguinte lista para configurar a identidade gerida para conjuntos de balanças de máquinas virtuais Azure (nas regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de gestor de recursos Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Microsoft Azure

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualizar | ![Disponível][check] |
| Utilizador atribuído | ![Disponível][check] | ![Disponível][check] | Pré-visualizar | ![Disponível][check] |

Consulte a seguinte lista para configurar a identidade gerida para as máquinas virtuais Azure (nas regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos de gestor de recursos Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [SDKs do Azure](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Construtor de imagem Azure VM

| Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Não Disponível | Não Disponível | Não Disponível | Não Disponível |
| Utilizador atribuído | [Disponível em regiões apoiadas](../../virtual-machines/windows/image-builder-overview.md#regions) | Não Disponível | Não Disponível | Não Disponível |

Para aprender a configurar a identidade gerida para O Azure VM Image Builder (em regiões onde disponível), consulte a visão geral do [Construtor de Imagens](../../virtual-machines/windows/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Serviço SignalR do Azure

Tipo de identidade gerido | Tudo geralmente disponível<br>Regiões Azure Globais | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistema atribuído | Pré-visualizar | Pré-visualizar | Não disponível | Pré-visualizar |
| Utilizador atribuído | Pré-visualizar | Pré-visualizar | Não disponível | Pré-visualizar |

Consulte a seguinte lista para configurar a identidade gerida para o Serviço Azure SignalR (nas regiões onde disponível):

- [Modelo Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços Azure que suportam a autenticação AD Azure

Os seguintes serviços apoiam a autenticação Azure AD, e foram testados com serviços de clientes que utilizam identidades geridas para recursos Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte a seguinte lista para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso através do portal Azure](howto-assign-access-portal.md)
- [Atribuir acesso via PowerShell](howto-assign-access-powershell.md)
- [Atribuir acesso via Azure CLI](howto-assign-access-CLI.md)
- [Atribuir acesso através do modelo do Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponível][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://management.microsoftazure.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponível][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha |  `https://vault.microsoftazure.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Disponível][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponível][check] |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-sql"></a>SQL do Azure

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponível][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://database.cloudapi.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Disponível][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponível][check] |
| Azure Government |  | Não Disponível |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponível][check] |
| Azure Government |  | ![Disponível][check] |
| Azure Alemanha |   | Não Disponível |
| Azure China 21Vianet |  | Não Disponível |









### <a name="azure-storage-blobs-and-queues"></a>Bolhas e filas de armazenamento Azure

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponível][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID do Recurso | Estado |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponível][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Disponível][check] |

> [!Note]
> O Microsoft Power BI também [suporta identidades geridas.](../../stream-analytics/powerbi-output-managed-identity.md)


[check]: media/services-support-managed-identities/check.png "Disponível"
