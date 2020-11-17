---
title: Restringir as permissões de acesso ao utilizador dos hóspedes - Azure Ative Directory / Microsoft Docs
description: Restringir as permissões de acesso ao utilizador dos hóspedes utilizando o portal Azure, PowerShell ou Microsoft Graph no Azure Ative Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac88e57ce0135295ac4b7078111102fa69e6838
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646598"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Restringir permissões de acesso a hóspedes (pré-visualização) no Azure Ative Directory

O Azure Ative Directory (Azure AD) permite-lhe restringir o que os utilizadores externos podem ver na sua organização em Azure AD. Os utilizadores de hóspedes estão definidos para um nível de permissão limitado por padrão em Azure AD, enquanto o padrão para os utilizadores membros é o conjunto completo de permissões de utilizador predefinidas. Esta é uma pré-visualização de um novo nível de permissão do utilizador convidado nas definições de colaboração externa da sua organização Azure AD para um acesso ainda mais restrito, pelo que as suas opções de acesso ao hóspede são agora:

Nível de permissão         | Nível de acesso
----------------         | ------------
O mesmo que os utilizadores membros     | Os hóspedes têm o mesmo acesso aos recursos Azure AD que os utilizadores membros
Acesso limitado (padrão) | Os hóspedes podem ver a adesão de todos os grupos não escondidos
**Acesso restrito (novo)**  | **Os hóspedes não podem ver a adesão de nenhum grupo**

Quando o acesso ao hóspede é restrito, os hóspedes podem ver apenas o seu próprio perfil de utilizador. A permissão para visualizar outros utilizadores não é permitida mesmo que o hóspede esteja a pesquisar pelo Nome Principal do Utilizador ou objectId. O acesso restrito também restringe os utilizadores convidados de verem a adesão de grupos em que se encontram. Para obter mais informações sobre as permissões gerais do utilizador por defeito, incluindo permissões de utilizador de hóspedes, consulte [quais são as permissões padrão do utilizador no Azure Ative Directory?](../fundamentals/users-default-permissions.md)

## <a name="permissions-and-licenses"></a>Permissões e licenças

Deve estar na função de Administrador Global para configurar as definições de colaboração externa. Não existem requisitos adicionais de licenciamento para restringir o acesso dos hóspedes.

## <a name="update-in-the-azure-portal"></a>Atualização no portal Azure

Fizemos alterações nos controlos existentes do portal Azure para permissões de utilizadores convidados.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com permissões de administrador global.
1. Na página geral do **Azure Ative Directory** para a sua organização, selecione **as definições do Utilizador**.
1. Em **utilizadores externos,** **selecione Gerir as definições de colaboração externa**.
1. Na página **de definições de colaboração externa,** o acesso ao utilizador do hóspede é restrito a propriedades e membros da sua própria opção **de objetos de diretório.**

    ![Página de definições de colaboração externa AZure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Selecione **Guardar**. As alterações podem demorar até 15 minutos a produzir efeitos para os utilizadores convidados.

## <a name="update-with-the-microsoft-graph-api"></a>Atualização com a API do Gráfico microsoft

Adicionámos uma nova API do Microsoft Graph para configurar permissões de hóspedes na sua organização AZure AD. As seguintes chamadas da API podem ser feitas para atribuir qualquer nível de permissão. O valor para o guestUserRoleId usado aqui é ilustrar a configuração do utilizador de hóspedes mais restrito. Para obter mais informações sobre a utilização do Microsoft Graph para definir permissões de hóspedes, consulte [o tipo de recurso de autorização](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Configurar pela primeira vez

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A resposta deve ser sucesso 204.

### <a name="updating-the-existing-value"></a>Atualização do valor existente

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A resposta deve ser sucesso 204.

### <a name="view-the-current-value"></a>Ver o valor atual

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Resposta de exemplo:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Atualização com cmdlets PowerShell

Com esta funcionalidade, adicionámos a capacidade de configurar as permissões restritas através de cmdlets PowerShell v2. Os cmdlets Get e set PowerShell foram publicados na versão 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Obtenha o comando: Get-AzureADMSAuthorizationPolicy

Exemplo:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Definir comando: Set-AzureADMSAuthorizationPolicy

Exemplo:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Deve introduzir autorizaçãoPolítica como identificação quando solicitado.

## <a name="supported-microsoft-365-services"></a>Serviços suportados pela Microsoft 365

### <a name="supported-services"></a>Serviços suportados

Por apoio, queremos dizer que a experiência é como esperado; especificamente, que é o mesmo que a experiência atual do hóspede.

- Teams
- Perspetivas (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>Serviços atualmente não suportados

O serviço sem suporte atual pode ter problemas de compatibilidade com a nova configuração de restrição de hóspedes.

- Formulários
- Planejador em Equipas
- Aplicativo de planejador
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

Pergunta | Resposta
-------- | ------
Onde se aplicam estas permissões? | Estas permissões de nível de diretório são aplicadas em todos os serviços e portais AZure AD, incluindo o Microsoft Graph, PowerShell v2, o portal Azure e o portal My Apps. Os serviços da Microsoft 365 que aproveitam os grupos microsoft 365 para cenários de colaboração também são afetados, nomeadamente o Outlook, Microsoft Teams e SharePoint.
Que partes do portal My Apps irá afetar esta funcionalidade? | A funcionalidade dos grupos no portal My Apps irá homenagear estas novas permissões. Isto inclui todos os caminhos para ver a lista de grupos e membros do grupo nas Minhas Apps. Não foram feitas alterações à disponibilidade de azulejos do grupo. A disponibilidade de azulejos de grupo ainda é controlada pela configuração do grupo existente no portal de administração Azure.
Estas permissões substituem as definições de hóspedes do SharePoint ou da Microsoft Teams? | Não. As definições existentes ainda controlam a experiência e o acesso nessas aplicações. Por exemplo, se vir problemas no SharePoint, verifique duas vezes as definições de partilha externa.
Quais são os problemas de compatibilidade conhecidos no Planner e no Yammer? | <li>Com permissões definidas para 'restrito', os hóspedes que iniciaram sessão na aplicação Planner ou que acedam ao Planner em Microsoft Teams não poderão aceder aos seus planos ou a quaisquer tarefas.<li>Com permissões definidas para 'restrito', os hóspedes que entrarem em Yammer não poderão sair do grupo.
As minhas permissões de hóspedes existentes serão alteradas no meu inquilino? | Não foram efetuadas alterações nas definições atuais. Mantemos a retrocompatibilidade com as definições existentes. Decide quando quer fazer mudanças.
Estas permissões serão definidas por defeito? | Não. As permissões por defeito existentes permanecem inalteradas. Pode configurar opcionalmente as permissões para serem mais restritivas.
Existem requisitos de licença para esta funcionalidade? | Não, não há novos requisitos de licenciamento com esta funcionalidade.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre as permissões existentes no Azure AD, veja [quais são as permissões padrão do utilizador no Azure Ative Directory?](../fundamentals/users-default-permissions.md)
- Para ver os métodos da Microsoft Graph API para restringir o acesso dos hóspedes, consulte [o tipo de recurso de autorizaçãoPolicy](/graph/api/resources/authorizationpolicy)
- Para revogar todo o acesso a um utilizador, consulte [o acesso do utilizador da Revoke em Azure AD](users-revoke-access.md)