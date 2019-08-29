---
title: Como usar o controle de acesso baseado em função no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como usar as funções internas e criar funções personalizadas no gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: e33d981429f0e79accbe47ea0edea5f3c7a2157b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072217"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como usar o controle de acesso baseado em função no gerenciamento de API do Azure

O gerenciamento de API do Azure depende do RBAC (controle de acesso baseado em função) do Azure para habilitar o gerenciamento de acesso refinado para serviços e entidades de gerenciamento de API (por exemplo, APIs e políticas). Este artigo fornece uma visão geral das funções internas e personalizadas no gerenciamento de API. Para obter mais informações sobre o gerenciamento de acesso no portal do Azure, consulte Introdução [ao gerenciamento de acesso no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Funções incorporadas

Atualmente, o gerenciamento de API fornece três funções internas e adicionará mais duas funções em breve. Essas funções podem ser atribuídas em escopos diferentes, incluindo assinatura, grupo de recursos e instância de gerenciamento de API individual. Por exemplo, se você atribuir a função "leitor de serviço de gerenciamento de API do Azure" a um usuário no nível do grupo de recursos, o usuário terá acesso de leitura a todas as instâncias de gerenciamento de API dentro do grupo de recursos. 

A tabela a seguir fornece breves descrições das funções internas. Você pode atribuir essas funções usando o portal do Azure ou outras ferramentas, incluindo o Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)e a [API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Para obter detalhes sobre como atribuir funções internas, consulte [usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Acesso de leitura<sup>[1]</sup> | Acesso de gravação<sup>[2]</sup> | Criação de serviço, exclusão, dimensionamento, VPN e configuração de domínio personalizado | Acesso ao portal do Publicador herdado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Colaborador do serviço de gerenciamento de API do Azure | ✓ | ✓ | ✓ | ✓ | Superusuário. Tem acesso completo a CRUD para serviços e entidades de gerenciamento de API (por exemplo, APIs e políticas). Tem acesso ao portal do Publicador herdado. |
| Leitor de serviço de gerenciamento de API do Azure | ✓ | | || Tem acesso somente leitura a entidades e serviços de gerenciamento de API. |
| Operador de serviço de gerenciamento de API do Azure | ✓ | | ✓ | | Pode gerenciar serviços de gerenciamento de API, mas não entidades.|
| Editor de serviço de gerenciamento de API do Azure<sup>*</sup> | ✓ | ✓ | |  | Pode gerenciar entidades de gerenciamento de API, mas não serviços.|
| Gerenciador de conteúdo do gerenciamento de API do Azure<sup>*</sup> | ✓ | | | ✓ | Pode gerenciar o portal do desenvolvedor. Acesso somente leitura a serviços e entidades.|

<sup>[1] acesso de leitura aos serviços e entidades de gerenciamento de API (por exemplo, APIs e políticas).</sup>

<sup>[2] acesso de gravação a entidades e serviços de gerenciamento de API, exceto as seguintes operações: criação, exclusão e dimensionamento da instância; Configuração de VPN; e configuração de domínio personalizado.</sup>

<sup>\*A função editor de serviço estará disponível depois de migrarmos toda a interface do usuário do administrador do portal do Publicador existente para a portal do Azure. A função Gerenciador de conteúdo estará disponível depois que o portal do Publicador for refatorado para conter apenas a funcionalidade relacionada ao gerenciamento do portal do desenvolvedor.</sup>  

## <a name="custom-roles"></a>Funções personalizadas

Se nenhuma das funções internas atender às suas necessidades específicas, as funções personalizadas poderão ser criadas para fornecer gerenciamento de acesso mais granular para entidades de gerenciamento de API. Por exemplo, você pode criar uma função personalizada que tem acesso somente leitura a um serviço de gerenciamento de API, mas só tem acesso de gravação a uma API específica. Para saber mais sobre as funções personalizadas, consulte [funções personalizadas no RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Para poder ver uma instância de gerenciamento de API no portal do Azure, uma função personalizada deve incluir a ```Microsoft.ApiManagement/service/read``` ação.

Quando você cria uma função personalizada, é mais fácil começar com uma das funções internas. Edite os atributos para adicionar **ações**, não **ações**ou **AssignableScopes**e, em seguida, salve as alterações como uma nova função. O exemplo a seguir começa com a função "leitor de serviço de gerenciamento de API do Azure" e cria uma função personalizada chamada "editor de API de calculadora". Você pode atribuir a função personalizada a uma API específica. Consequentemente, essa função só tem acesso a essa API. 

```powershell
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

O artigo [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) contém a lista de permissões que podem ser concedidas no nível de gerenciamento de API.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o controle de acesso baseado em função no Azure, consulte os seguintes artigos:
  * [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
  * [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)
  * [Funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md)
  * [Operações do provedor de recursos Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)