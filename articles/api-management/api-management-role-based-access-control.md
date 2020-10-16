---
title: Como utilizar Role-Based Controlo de Acesso na Gestão API da Azure ! Microsoft Docs
description: Saiba como usar as funções incorporadas e crie funções personalizadas na Azure API Management
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
ms.openlocfilehash: faef2721b48ffab12264c585d2dec55ab9334016
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015294"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como utilizar o Controlo de Acesso Baseado em Funções na Gestão de API do Azure

A Azure API Management conta com o controlo de acesso baseado em funções Azure (Azure RBAC) para permitir uma gestão de acessos finos para serviços e entidades de Gestão de API (por exemplo, APIs e políticas). Este artigo dá-lhe uma visão geral das funções integradas e personalizadas na Gestão da API. Para obter mais informações sobre a gestão de acessos no portal Azure, consulte [Começar com a gestão de acessos no portal Azure.](../role-based-access-control/overview.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Funções incorporadas

A API Management oferece atualmente três funções integradas e irá adicionar mais duas funções num futuro próximo. Estas funções podem ser atribuídas em diferentes âmbitos, incluindo subscrição, grupo de recursos e instância individual de Gestão de API. Por exemplo, se atribuir a função "API Management Service Reader" a um utilizador ao nível do grupo de recursos, então o utilizador leu o acesso a todas as instâncias de Gestão de API dentro do grupo de recursos. 

A tabela a seguir apresenta breves descrições das funções incorporadas. Pode atribuir estas funções utilizando o portal Azure ou outras ferramentas, incluindo Azure [PowerShell,](../role-based-access-control/role-assignments-powershell.md) [Azure CLI](../role-based-access-control/role-assignments-cli.md)e [REST API](../role-based-access-control/role-assignments-rest.md). Para obter detalhes sobre como atribuir funções incorporadas, consulte utilizar atribuições de [funções para gerir o acesso aos seus recursos de subscrição Azure](../role-based-access-control/role-assignments-portal.md).

| Função          | Ler acesso<sup>[1]</sup> | Escrever acesso<sup>[2]</sup> | Criação de serviço, eliminação, escala, VPN e configuração de domínio personalizado | Acesso ao portal da editora | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Colaborador do Serviço de Gestão da API | ✓ | ✓ | ✓ | ✓ | Super utilizador. Tem acesso total da CRUD a serviços e entidades de Gestão de API (por exemplo, APIs e políticas). Tem acesso ao portal da editora. |
| Leitor de Serviço de Gestão da API | ✓ | | || Tem acesso apenas de leitura a serviços e entidades de Gestão da API. |
| Operador de serviço de gestão da API | ✓ | | ✓ | | Pode gerir os serviços de Gestão da API, mas não entidades.|
| Editor de Serviço de Gestão da API<sup>*</sup> | ✓ | ✓ | |  | Pode gerir entidades de Gestão de API, mas não serviços.|
| Gestor de Conteúdos de Gestão da API<sup>*</sup> | ✓ | | | ✓ | Pode gerir o portal de desenvolvedores. Acesso apenas de leitura a serviços e entidades.|

<sup>[1] Leia o acesso aos serviços e entidades de Gestão da API (por exemplo, APIs e políticas).</sup>

<sup>[2] Escrever acesso a serviços e entidades de Gestão da API, com exceção das seguintes operações: criação, supressão e escalação de casos; Configuração VPN; e configuração personalizada do domínio.</sup>

<sup>\* O papel de Editor de Serviço estará disponível depois de migrarmos toda a UI administrador do portal de editores existente para o portal Azure. A função de Gestor de Conteúdos estará disponível depois de o portal da editora ser refacado para conter apenas a funcionalidade relacionada com a gestão do portal do desenvolvedor.</sup>  

## <a name="custom-roles"></a>Funções personalizadas

Se nenhuma das funções incorporadas atender às suas necessidades específicas, podem ser criadas funções personalizadas para fornecer uma maior gestão de acesso granular às entidades de Gestão de API. Por exemplo, pode criar uma função personalizada que tenha acesso apenas de leitura a um serviço de Gestão API, mas que apenas tenha acesso a uma API específica. Para saber mais sobre papéis personalizados, consulte [os papéis personalizados no Azure RBAC.](../role-based-access-control/custom-roles.md) 

> [!NOTE]
> Para poder ver uma instância de Gestão da API no portal Azure, um papel personalizado deve incluir a ```Microsoft.ApiManagement/service/read``` ação.

Quando se cria um papel personalizado, é mais fácil começar com um dos papéis incorporados. Editar os atributos para adicionar **Ações**, **NotActions**ou **Atribuidores**, e, em seguida, guardar as alterações como um novo papel. O exemplo a seguir começa com o papel "API Management Service Reader" e cria uma função personalizada chamada "Editor API calculadora". Pode atribuir o papel personalizado a uma API específica. Consequentemente, esta função só tem acesso a essa API. 

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

O artigo de operações do [fornecedor de recursos Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) contém a lista de permissões que podem ser concedidas ao nível de Gestão da API.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Role-Based Controlo de Acesso em Azure, consulte os seguintes artigos:
  * [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
  * [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)
  * [Papéis personalizados em Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operações do fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
