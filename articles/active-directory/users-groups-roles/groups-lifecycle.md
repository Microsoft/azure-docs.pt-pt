---
title: Definir expiração para grupos do Office 365 – Azure Active Directory | Microsoft Docs
description: Como configurar a expiração para grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3cef2bd16907de6e60db2678516f70346a20285
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803605"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurar a política de expiração para grupos do Office 365

Este artigo mostra como gerenciar o ciclo de vida dos grupos do Office 365 definindo uma política de expiração para eles. Você pode definir a política de expiração somente para grupos do Office 365 no Azure Active Directory (AD do Azure).

Depois de definir um grupo para expirar:

- Grupos com atividades do usuário são renovados automaticamente à medida que a expiração é próxima
- Os proprietários do grupo são notificados para renovar o grupo, se o grupo não for renovado automaticamente
- Qualquer grupo que não é renovado é excluído
- Qualquer grupo do Office 365 excluído pode ser restaurado dentro de 30 dias por proprietários do grupo ou pelo administrador

As ações folloing resultarão na renovação automática de um grupo:

- SharePoint – exibir, editar, baixar, mover, compartilhar e carregar arquivos
- Outlook-ingressar no grupo, mensagem de grupo de leitura/gravação e como uma mensagem
- Equipes-visite um canal de equipes

Atualmente, apenas uma política de expiração pode ser configurada para grupos do Office 365 num inquilino.

> [!NOTE]
> Configurar e usar a política de expiração para grupos do Office 365 exige que você tenha, mas não necessariamente, atribuir Azure AD Premium licenças para os membros de todos os grupos aos quais a política de expiração é aplicada.

Para obter informações sobre como baixar e instalar os cmdlets do PowerShell do Azure AD, consulte [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Funções e permissões

As funções a seguir podem configurar e usar a expiração para grupos do Office 365 no Azure AD.

Função | Permissões
-------- | --------
Administrador global ou administrador de usuários | Pode criar, ler, atualizar ou excluir as configurações de política de expiração dos grupos do Office 365<br>Pode renovar qualquer grupo do Office 365
Utilizador | Pode renovar um grupo do Office 365 que ele possui<br>Pode restaurar um grupo do Office 365 que ele possui<br>Pode ler as configurações da política de expiração

Para obter mais informações sobre permissões para restaurar um grupo excluído, consulte [restaurar um grupo do Office 365 excluído no Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Abra o [centro de administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador global em sua organização do Azure AD.

2. Selecione **grupos**e, em seguida, selecione **expiração** para abrir as configurações de expiração.
  
   ![Configurações de expiração para grupos](./media/groups-lifecycle/expiration-settings.png)

3. Na página **expiração** , você pode:

  - Defina o tempo de vida do grupo em dias. Você pode selecionar um dos valores predefinidos ou um valor personalizado (deve ser 31 dias ou mais).
  - Especifique um endereço de email onde as notificações de renovação e expiração devem ser enviadas quando um grupo não tiver proprietário.
  - Selecione quais grupos do Office 365 expiram. Você pode definir a expiração para:
    - **Todos** Grupos do Office 365
    - Uma lista de grupos do Office 365 **selecionados**
    - **Nenhum** para restringir a expiração de todos os grupos
  - Salve as configurações quando terminar selecionando **salvar**.

> [!NOTE]
> Quando você configura pela primeira vez, todos os grupos que são mais antigos que o intervalo de expiração são definidos como 35 dias até a expiração, a menos que o grupo seja renovado automaticamente ou o proprietário o renova. 
>
> Quando um grupo dinâmico é excluído e restaurado, ele é visto como um novo grupo e populado novamente de acordo com a regra. Esse processo pode levar até 24 horas.
>
> Os avisos de expiração para grupos usados em equipes são exibidos no feed proprietários de equipes.

## <a name="email-notifications"></a>Notificações por e-mail

Se os grupos não forem renovados automaticamente, as notificações por email, como este, serão enviadas para os proprietários do grupo do Office 365 30 dias, 15 dias e 1 dia antes da expiração do grupo. O idioma do email é determinado pela configuração de idioma preferencial do proprietário de grupos ou da linguagem do Azure AD. Se o proprietário do grupo tiver definido um idioma preferencial ou se vários proprietários tiverem o mesmo idioma preferencial, esse idioma será usado. Para todos os outros casos, a configuração de idioma do Azure AD é usada.

![Notificações por email de expiração](./media/groups-lifecycle/expiration-notification.png)

No email de notificação do **grupo de renovação** , os proprietários do grupo podem acessar diretamente a página de detalhes do grupo no painel de acesso. Lá, os usuários podem obter mais informações sobre o grupo, como sua descrição, quando ele foi renovado pela última vez, quando ele irá expirar e também a capacidade de renovar o grupo. A página de detalhes do grupo agora também inclui links para os recursos do grupo do Office 365, para que o proprietário do grupo possa exibir convenientemente o conteúdo e a atividade em seu grupo.

Quando um grupo expira, o grupo é excluído um dia após a data de expiração. Uma notificação por email, como esta, é enviada para os proprietários do grupo do Office 365 informando sobre a expiração e a exclusão subsequente do grupo do Office 365.

![Notificações de email de exclusão de grupo](./media/groups-lifecycle/deletion-notification.png)

O grupo pode ser restaurado dentro de 30 dias após sua exclusão, selecionando **restaurar grupo** ou usando cmdlets do PowerShell, conforme descrito em [restaurar um grupo excluído do Office 365 no Azure Active Directory](groups-restore-deleted.md). Observe que o período de restauração do grupo de 30 dias não é personalizável.

Se o grupo que você está restaurando contém documentos, sites do SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar totalmente o grupo e seu conteúdo.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Como recuperar a data de validade do grupo do Office 365
Além do painel de acesso em que os usuários podem exibir detalhes do grupo, incluindo a data de expiração e a data da última renovação, a data de validade de um grupo do Office 365 pode ser recuperada de Microsoft Graph API REST beta. expirationDateTime como uma propriedade de grupo foi habilitada no Microsoft Graph beta. Ele pode ser recuperado com uma solicitação GET. Para obter mais detalhes, consulte [Este exemplo](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Para gerenciar as associações de grupo no painel de acesso, "restringir o acesso a grupos no painel de acesso" precisa ser definido como "não" na configuração geral de grupos de Azure Active Directory.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como a expiração do grupo do Office 365 funciona com uma caixa de correio em retenção legal
Quando um grupo expira e é excluído, 30 dias depois de excluir os dados do grupo de aplicativos como o Planner, sites ou equipes são excluídos permanentemente, mas a caixa de correio do grupo que está em retenção legal é mantida e não é excluída permanentemente. O administrador pode usar os cmdlets do Exchange para restaurar a caixa de correio para buscar os dados. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Como a expiração do grupo do Office 365 funciona com a política de retenção
A política de retenção é configurada por meio do centro de segurança e conformidade. Se você configurou uma política de retenção para grupos do Office 365, quando um grupo expira e é excluído, as conversas de grupo na caixa de correio do grupo e os arquivos no site do grupo são mantidos no contêiner de retenção para o número específico de dias definidos na retenção regras. Os usuários não verão o grupo ou seu conteúdo após a expiração, mas podem recuperar os dados de site e caixa de correio via e-Discovery.

## <a name="powershell-examples"></a>Exemplos do PowerShell
Aqui estão exemplos de como você pode usar os cmdlets do PowerShell para definir as configurações de expiração dos grupos do Office 365 na sua organização do Azure AD:

1. Instale o módulo do PowerShell v 2.0 e entre no prompt do PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Definir as configurações de expiração use o cmdlet New-AzureADMSGroupLifecyclePolicy para definir o tempo de vida de todos os grupos do Office 365 na organização do Azure AD como 365 dias. As notificações de renovação para grupos do Office 365 sem proprietários serão enviadas para 'emailaddress@contoso.com'
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperar a política existente Get-AzureADMSGroupLifecyclePolicy: esse cmdlet recupera as configurações atuais de expiração do grupo do Office 365 que foram configuradas. Neste exemplo, você pode ver:

   - A ID da política
   - O tempo de vida de todos os grupos do Office 365 na organização do Azure AD é definido como 365 dias
   - As notificações de renovação para grupos do Office 365 sem proprietários serão enviadas para 'emailaddress@contoso.com'.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Atualizar a política existente Set-AzureADMSGroupLifecyclePolicy: Este cmdlet é usado para atualizar uma política existente. No exemplo a seguir, o tempo de vida do grupo na política existente é alterado de 365 dias para 180 dias.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adicionar grupos específicos à política Add-AzureADMSLifecyclePolicyGroup: esse cmdlet adiciona um grupo à política de ciclo de vida. Em forma de exemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Remover a política existente remove-AzureADMSGroupLifecyclePolicy: Este cmdlet exclui as configurações de expiração do grupo do Office 365, mas requer a ID da política. Isso desabilitará a expiração dos grupos do Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Os cmdlets a seguir podem ser usados para configurar a política mais detalhadamente. Para obter mais informações, consulte a [documentação do PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Redefinir-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passos seguintes

Esses artigos fornecem informações adicionais sobre grupos do Azure AD.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
