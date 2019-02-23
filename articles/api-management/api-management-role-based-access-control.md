---
title: Como utilizar o controlo de acesso baseado em funções na gestão de API do Azure | Documentos da Microsoft
description: Saiba como utilizar as funções incorporadas e criar funções personalizadas na gestão de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: d2d62dea683d3e42411d526fe002596e6473b4a9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736673"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como utilizar o controlo de acesso baseado em funções na gestão de API do Azure
Gestão de API do Azure baseia-se no controlo de acesso de controlo (RBAC) para ativar a gestão de acessos detalhada para os serviços de gestão de API e entidades (por exemplo, APIs e as políticas). Este artigo fornece uma descrição geral das funções incorporadas e personalizadas na gestão de API. Para obter mais informações sobre a gestão de acesso no portal do Azure, consulte [introdução à gestão de acesso no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Funções incorporadas
Atualmente, a gestão de API fornece três funções incorporadas e adicionar duas funções mais no futuro próximo. Estas funções podem ser atribuídas em escopos diferentes, incluindo a subscrição, grupo de recursos e instância de gestão de API individual. Por exemplo, se atribuir a função "Leitor do serviço de gestão de API do Azure" para um utilizador ao nível do grupo de recursos, em seguida, o utilizador tem acesso de leitura para todas as instâncias de gestão de API no interior do grupo de recursos. 

A tabela seguinte fornece breves descrições das funções incorporadas. Pode atribuir essas funções utilizando o portal do Azure ou outras ferramentas, incluindo o Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), e [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Para obter detalhes sobre como atribuir funções incorporadas, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Função          | Acesso de leitura<sup>[1]</sup> | Acesso de escrita<sup>[2]</sup> | Criação do serviço, eliminação, dimensionamento, VPN e a configuração de domínio personalizado | Acesso ao portal do publicador de legado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Contribuinte de serviços de gestão de API do Azure | ✓ | ✓ | ✓ | ✓ | Superutilizador. Tem acesso total de CRUD para serviços de gestão de API e entidades (por exemplo, APIs e as políticas). Tem acesso ao portal do publicador de legado. |
| Leitor de serviço de gestão de API do Azure | ✓ | | || Tem acesso só de leitura para os serviços de gestão de API e entidades. |
| Operador de serviço de gestão de API do Azure | ✓ | | ✓ | | Pode gerir os serviços de gestão de API, mas não as entidades.|
| Editor do serviço de gestão de API do Azure<sup>*</sup> | ✓ | ✓ | |  | Pode gerir entidades de gestão de API, mas não os serviços.|
| Gestor de conteúdo de gestão de API do Azure<sup>*</sup> | ✓ | | | ✓ | Pode gerir o portal do programador. Acesso só de leitura aos serviços e entidades.|

<sup>[1] acesso de leitura para os serviços de gestão de API e entidades (por exemplo, APIs e as políticas).</sup>

<sup>[2] acesso de escrita para serviços de gestão de API e entidades, exceto as seguintes operações: criação, eliminação e dimensionamento; da instância Configuração de VPN e a configuração do domínio personalizado.</sup>

<sup>\* A função de Editor do serviço estará disponível depois vamos migrar todos os o administrador da interface do Usuário do portal do publicador existente ao portal do Azure. A função de Gestor de conteúdos vai estar disponível depois do portal do Editor é refatorizado para conter apenas as funcionalidades relacionadas com a gestão do portal do programador.</sup>  

## <a name="custom-roles"></a>Funções personalizadas
Se nenhuma das funções incorporadas às suas necessidades específicas, funções personalizadas podem ser criadas para fornecer gestão de acesso mais granular para entidades de gestão de API. Por exemplo, pode criar uma função personalizada que tem acesso só de leitura a um serviço de gestão de API, mas apenas tem acesso de escrita para uma API específica. Para saber mais sobre as funções personalizadas, veja [funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

Quando cria uma função personalizada, é mais fácil de começar com uma das funções incorporadas. Editar os atributos a adicionar **ações**, **NotActions**, ou **AssignableScopes**e, em seguida, guarde as alterações como uma nova função. O exemplo seguinte começa com a função "Leitor do serviço de gestão de API do Azure" e cria uma função personalizada chamada "Editor de API de calculadora." Pode atribuir a função personalizada para uma API específica. Por conseqüência, esta função só tem acesso a essa API. 

```
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

O [operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) artigo contém a lista de permissões que podem ser concedidas no nível de gestão de API.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o controlo de acesso baseado em funções no Azure, veja os artigos seguintes:
  * [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
  * [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)
  * [Funções personalizadas no Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)

