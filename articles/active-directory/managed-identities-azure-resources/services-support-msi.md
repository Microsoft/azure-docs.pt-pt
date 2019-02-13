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
ms.openlocfilehash: 2aa25d2e0fe4700eab69d383d1ec8b57fca86bd4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201555"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que suportam identidades geridas para recursos do Azure

Identidades geridas para recursos do Azure fornecem serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Utilizar uma identidade gerida, pode autenticar a qualquer serviço que suporta a autenticação do Azure AD sem ter credenciais em seu código. Estamos no processo de integração de identidades geridas para recursos do Azure e a autenticação do Azure AD no Azure. Verificar back, muitas vezes, a existência de atualizações.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que suportam identidades geridas para os recursos do Azure

Os seguintes serviços do Azure suportam identidades geridas para recursos do Azure:

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

|Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização

Consulte a lista seguinte para configurar a identidade gerida para máquinas de virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de Máquina Virtual do Azure

|Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Pré-visualização | Pré-visualização | Pré-visualização |
| Utilizador atribuído | Pré-visualização | Pré-visualização | Pré-visualização | Pré-visualização

Consulte a lista seguinte para configurar a identidade gerida para conjuntos de dimensionamento de máquinas virtuais do Azure (em regiões onde disponível):

- [Portal do Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

|Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida para o serviço de aplicações do Azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Pré-visualização | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida para as funções do Azure (em regiões onde disponível):

- [Portal do Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI do Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Disponível | Disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida para o Azure Logic Apps (nas regiões onde disponível):

- [Portal do Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modelo do Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida para o Azure Data Factory V2 (nas regiões onde disponível):

- [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>API Management do Azure

Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Disponível | Disponível | Não disponível | Não disponível |
| Utilizador atribuído | Não disponível | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida para a gestão de API do Azure (em regiões onde disponível):

- [Modelo do Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidade gerido |  Disponíveis em geral<br>Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Sistema atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |
| Utilizador atribuído | Linux: Pré-visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível

Consulte a lista seguinte para configurar a identidade gerida do Azure Container Instances (nas regiões onde disponível):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Suporte do Azure AD que a autenticação de serviços do Azure

Os seguintes serviços de suportam de autenticação do Azure AD e foram testados com serviços de cliente que utilizam identidades geridas para recursos do Azure.

| Serviço | ID do Recurso | Estado | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponível | [Portal do Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[CLI do Azure](howto-assign-access-CLI.md) <br>[Modelo do Azure Resource Manager](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | Disponível |  
| Azure Data Lake | `https://datalake.azure.net/` | Disponível |
| SQL do Azure | `https://database.windows.net/` | Disponível |
| Azure Event Hubs | `https://eventhubs.azure.net` | Pré-visualização |
| Service Bus do Azure | `https://servicebus.azure.net` | Pré-visualização |
| Storage do Azure | `https://storage.azure.com/` | Pré-visualização |
