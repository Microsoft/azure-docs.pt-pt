---
title: Como utilizar o Controlo de Acesso baseado em funções na Gestão da API Azure [ Microsoft Docs
description: Aprenda a usar as funções incorporadas e crie papéis personalizados na Azure API Management
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
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430621"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure

A Azure API Management conta com o Azure Role-Based Access Control (RBAC) para permitir a gestão de acesso sinuoso para serviços e entidades de Gestão de API (por exemplo, APIs e políticas). Este artigo dá-lhe uma visão geral das funções incorporadas e personalizadas na Gestão aPI. Para obter mais informações sobre a gestão de acessos no portal Azure, consulte [Iniciar-se com a gestão de acesso no portal Azure.](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Funções incorporadas

A API Management atualmente fornece três funções incorporadas e irá adicionar mais duas funções num futuro próximo. Estas funções podem ser atribuídas em diferentes âmbitos, incluindo subscrição, grupo de recursos e instância individual de Gestão de API. Por exemplo, se atribuir a função "API Management Service Reader" a um utilizador ao nível do grupo de recursos, então o utilizador leu o acesso a todas as instâncias de Gestão de API dentro do grupo de recursos. 

A tabela seguinte apresenta breves descrições das funções incorporadas. Pode atribuir estas funções utilizando o portal Azure ou outras ferramentas, incluindo o Azure [PowerShell,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) [O ClI Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)e o [REST API.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) Para mais detalhes sobre como atribuir funções incorporadas, consulte As atribuições de [funções de utilização para gerir o acesso aos seus recursos de subscrição Azure.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

| Função          | Ler acesso<sup>[1]</sup> | Escrever acesso<sup>[2]</sup> | Criação de serviços, supressão, escala, VPN e configuração de domínio personalizado | Acesso ao portal da editora legado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Colaborador do Serviço de Gestão da API | ✓ | ✓ | ✓ | ✓ | Super utilizador. Tem acesso total à API Management serviços e entidades (por exemplo, APIs e políticas). Tem acesso ao portal da editora. |
| Leitor de Serviço de Gestão API | ✓ | | || Tem acesso apenas a serviços e entidades de Gestão API. |
| Operador de Serviço de Gestão API | ✓ | | ✓ | | Pode gerir os serviços de Gestão aPI, mas não entidades.|
| Editor de Serviço de Gestão API<sup>*</sup> | ✓ | ✓ | |  | Pode gerir entidades de Gestão aPI, mas não serviços.|
| Gestor de Conteúdos de Gestão API<sup>*</sup> | ✓ | | | ✓ | Pode gerir o portal de desenvolvimento. Acesso apenas a leitura a serviços e entidades.|

<sup>[1] Ler o acesso aos serviços e entidades de Gestão da API (por exemplo, APIs e políticas).</sup>

<sup>[2] Escrever acesso aos serviços e entidades de Gestão da API, com exceção das seguintes operações: criação de exemplos, supressão e escalagem; Configuração VPN; e configuração de domínio personalizado.</sup>

<sup>\*O papel de Editor de Serviço estará disponível depois de migrarmos toda a UI administradora do portal de editores existente para o portal Azure. A função De Gestor de Conteúdos estará disponível após o portal da editora ser refactorado para conter apenas funcionalidaderelacionada com a gestão do portal de desenvolvimento.</sup>  

## <a name="custom-roles"></a>Funções personalizadas

Se nenhuma das funções incorporadas atender às suas necessidades específicas, podem ser criadas funções personalizadas para fornecer uma gestão de acesso mais granular para entidades de Gestão de API. Por exemplo, pode criar uma função personalizada que tenha acesso apenas a um serviço de Gestão API, mas que só tenha acesso por escrito a uma API específica. Para saber mais sobre papéis personalizados, consulte [papéis personalizados no Azure RBAC.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 

> [!NOTE]
> Para poder ver uma instância de Gestão API no portal ```Microsoft.ApiManagement/service/read``` Azure, um papel personalizado deve incluir a ação.

Quando se cria um papel personalizado, é mais fácil começar com um dos papéis incorporados. Editar os atributos para adicionar **Ações,** **NotActions,** ou **Assícíveis,** e, em seguida, guardar as alterações como uma nova função. O exemplo seguinte começa com o papel "API Management Service Reader" e cria um papel personalizado chamado "Editor da Calculadora API". Pode atribuir o papel personalizado a uma API específica. Por conseguinte, este papel só tem acesso a essa API. 

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

O artigo de operações do gestor de [recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) contém a lista de permissões que podem ser concedidas a nível de Gestão de API.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Controlo de Acesso baseado em Funções em Azure, consulte os seguintes artigos:
  * [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
  * [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)
  * [Papéis personalizados em Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operações de fornecedor de recursos do Gestor de Recursos Azure](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
