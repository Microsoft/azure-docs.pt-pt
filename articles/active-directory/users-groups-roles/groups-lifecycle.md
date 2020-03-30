---
title: Definir expiração para o Office 365 grupos - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: Como configurar a expiração para os grupos do Office 365 no Diretório Ativo azure
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048252"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configure a política de expiração para os grupos do Office 365

Este artigo diz-lhe como gerir o ciclo de vida dos grupos do Office 365, estabelecendo-lhes uma política de validade. Só pode definir a política de expiração para os grupos Office 365 no Azure Ative Directory (Azure AD).

Uma vez definido um grupo para expirar:

- Os grupos com atividades do utilizador são automaticamente renovados à medida que a expiração se aproxima.
- Os proprietários do grupo são notificados para renovar o grupo, caso o grupo não seja renovado automaticamente.
- Qualquer grupo que não seja renovado é eliminado.
- Qualquer grupo do Office 365 que seja eliminado pode ser restaurado no prazo de 30 dias pelos proprietários do grupo ou pelo administrador.

Atualmente, apenas uma política de expiração pode ser configurada para todos os grupos do Office 365 numa organização da AD Azure.

> [!NOTE]
> Configurar e utilizar a política de expiração para os grupos office 365 requer que possua, mas não necessariamente atribuir licenças Azure AD Premium para os membros de todos os grupos aos quais a política de expiração é aplicada.

Para obter informações sobre como descarregar e instalar os cmdlets Azure AD PowerShell, consulte [o Azure Ative Directory PowerShell para o Gráfico 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Renovação automática baseada na atividade

Com a inteligência da AD Azure, os grupos são agora automaticamente renovados com base em se foram recentemente utilizados. Esta funcionalidade elimina a necessidade de ação manual por parte dos proprietários do grupo, porque é baseada na atividade do utilizador em grupos em todo o Office 365 serviços como Outlook, SharePoint ou Teams. Por exemplo, se um proprietário ou membro do grupo fizer algo como carregar um documento no SharePoint, visitar um canal de Equipas ou enviar um e-mail para o grupo no Outlook, o grupo é automaticamente renovado e o proprietário não recebe nenhuma notificação de renovação.

### <a name="activities-that-automatically-renew-group-expiration"></a>Atividades que renovam automaticamente a expiração do grupo

As seguintes ações de utilizador causam renovação automática do grupo:

- SharePoint: Ver, editar, descarregar, mover, partilhar ou carregar ficheiros
- Outlook: Junte-se ao grupo, leia/escreva mensagem de grupo a partir do espaço do grupo, como uma mensagem (no Outlook Web Access)
- Equipas: Visite um canal de equipas

### <a name="auditing-and-reporting"></a>Auditoria e relatórios

Os administradores podem obter uma lista de grupos renovados automaticamente dos registos de auditoria de atividade em Azure AD.

![Renovação automática de grupos com base na atividade](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Seguem-se funções que podem configurar e utilizar a expiração para os grupos office 365 em Azure AD.

Função | Permissões
-------- | --------
Administrador global, administrador do Grupo ou administrador de utilizador | Pode criar, ler, atualizar ou eliminar as definições de política de expiração de grupos do Office 365<br>Pode renovar qualquer grupo do Office 365
Utilizador | Pode renovar um grupo de Escritório 365 que possui<br>Pode restaurar um grupo do Office 365 que eles possuem<br>Pode ler as definições da política de expiração

Para obter mais informações sobre permissões para restaurar um grupo eliminado, consulte [Restaurar um grupo eliminado do Office 365 no Diretório Ativo Azure](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Abra o centro de [administração azure ad](https://aad.portal.azure.com) com uma conta que é um administrador global na sua organização Azure AD.

2. Selecione **Grupos,** então selecione **Expiração** para abrir as definições de expiração.
  
   ![Definições de expiração para grupos](./media/groups-lifecycle/expiration-settings.png)

3. Na página **Expiração,** pode:

    - Estabeleça a vida em grupo em dias. Pode selecionar um dos valores predefinidos, ou um valor personalizado (deve ser de 31 dias ou mais).
    - Especifique um endereço de e-mail onde as notificações de renovação e expiração devem ser enviadas quando um grupo não tem proprietário.
    - Selecione quais os grupos do Office 365 expirados. Pode definir a expiração para:
      - **Todos** Escritório 365 grupos
      - Uma lista de grupos **selecionados** do Office 365
      - **Nenhum** para restringir a expiração para todos os grupos
    - Guarde as definições quando terminar selecionando **Save**.

> [!NOTE]
> - Quando configura a expiração pela primeira vez, os grupos mais antigos do que o intervalo de validade são definidos para 35 dias até expirar, a menos que o grupo seja renovado automaticamente ou o proprietário o renove.
> - Quando um grupo dinâmico é apagado e restaurado, é visto como um novo grupo e repovoado de acordo com a regra. Este processo pode demorar até 24 horas.
> - Os avisos de validade para grupos utilizados em Equipas aparecem no feed dos Proprietários de Equipas.

## <a name="email-notifications"></a>Notificações por e-mail

Se os grupos não forem automaticamente renovados, notificações por e-mail como esta são enviadas ao Office 365 proprietários do grupo 30 dias, 15 dias e 1 dia antes da expiração do grupo. A linguagem do e-mail é determinada pela linguagem preferida do proprietário dos grupos ou pela definição de linguagem AD Azure. Se o proprietário do grupo definiu uma língua preferida, ou vários proprietários têm a mesma língua preferida, então essa língua é usada. Para todos os outros casos, a definição de linguagem Azure AD é usada.

![Notificações de e-mail de expiração](./media/groups-lifecycle/expiration-notification.png)

A partir do e-mail de notificação do **grupo Renovar,** os proprietários do grupo podem aceder diretamente à página de dados do grupo no Painel de [Acesso](https://account.activedirectory.windowsazure.com/r#/applications). Aí, os utilizadores podem obter mais informações sobre o grupo, como a sua descrição, quando foi renovado pela última vez, quando expirará, e também a capacidade de renovar o grupo. A página de detalhes do grupo agora também inclui links para os recursos do grupo Office 365, para que o proprietário do grupo possa ver convenientemente o conteúdo e atividade no seu grupo.

Quando um grupo expira, o grupo é eliminado um dia após a data de validade. Uma notificação por e-mail como esta é enviada ao Office 365 proprietários do grupo informando-os sobre a expiração e subsequente supressão do seu grupo Office 365.

![Notificações de e-mail de eliminação de grupo](./media/groups-lifecycle/deletion-notification.png)

O grupo pode ser restaurado no prazo de 30 dias após a sua eliminação selecionando o **grupo Restore** ou utilizando cmdlets PowerShell, conforme descrito no [Restore a eliminado Office 365 group in Azure Ative Directory](groups-restore-deleted.md). Por favor, note que o período de restauração do grupo de 30 dias não é personalizável.

Se o grupo que está a restaurar contém documentos, sites SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar totalmente o grupo e o seu conteúdo.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Como recuperar a data de validade do grupo 365 do Office 365

Além do Painel de Acesso, onde os utilizadores podem ver os detalhes do grupo, incluindo a data de validade e a última data renovada, a data de validade de um grupo do Office 365 pode ser recuperada a partir do Microsoft Graph REST API Beta. expiraçãoDateTime como uma propriedade de grupo foi ativado no Microsoft Graph Beta. Pode ser recuperado com um pedido GET. Para mais informações, consulte [este exemplo.](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)

> [!NOTE]
> Para gerir os membros do grupo no Painel de Acesso, é necessário definir o "Restringir o acesso aos grupos no Painel de Acesso" para "Não" na Definição Geral dos Grupos de DiretórioActivo do Azure.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como a expiração do grupo Office 365 funciona com uma caixa de correio em espera legal

Quando um grupo expira e é eliminado, então 30 dias após a eliminação os dados do grupo de apps como Planner, Sites ou Equipas são permanentemente eliminados, mas a caixa de correio de grupo que está em espera legal é mantida e não é permanentemente eliminada. O administrador pode utilizar os cmdlets de troca para restaurar a caixa de correio para recolher os dados.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Como o Office 365 expiração do grupo funciona com a política de retenção

A política de retenção é configurada através do Centro de Segurança e Conformidade. Se tiver criado uma política de retenção para os grupos do Office 365, quando um grupo expira e é eliminado, as conversas em grupo na caixa de correio do grupo e os ficheiros no site do grupo são mantidos no contentor de retenção pelo número específico de dias definidos na retenção política. Os utilizadores não verão o grupo ou o seu conteúdo após a expiração, mas podem recuperar os dados do site e da caixa de correio através da descoberta de e-mails.

## <a name="powershell-examples"></a>Exemplos powerShell

Aqui estão exemplos de como pode utilizar cmdlets PowerShell para configurar as definições de expiração para os grupos Office 365 na sua organização Azure AD:

1. Instale o módulo PowerShell v2.0 e inscreva-se no pedido powerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configurar as definições de validade Utilize o cmdlet New-AzureADMSGroupLifecycle Policy para definir o tempo de vida de todos os grupos do Office 365 na organização Azure AD para 365 dias. As notificações de renovação para os gruposemailaddress@contoso.comdo Office 365 sem proprietários serão enviadas para »
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recupere a política existente Get-AzureADMSGroupLifecyclePolicy: Este cmdlet recupera as atuais definições de expiração do grupo Office 365 que foram configuradas. Neste exemplo, pode ver:

   - A identificação da política
   - A vida útil para todos os grupos do Office 365 na organização Azure AD está marcada para 365 dias
   - As notificações de renovação para os gruposemailaddress@contoso.comdo Office 365 sem proprietários serão enviadas para «».
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Atualizar a política existente Set-AzureADMSGroupLifecyclePolicy: Este cmdlet é utilizado para atualizar uma política existente. No exemplo abaixo, o grupo vitalício na política existente é alterado de 365 dias para 180 dias.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adicione grupos específicos à política Add-AzureADMSLifecyclePolicyGroup: Este cmdlet adiciona um grupo à política do ciclo de vida. Em forma de exemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Remova a política existente Remove-AzureADMSGroupLifecyclePolicy: Este cmdlet elimina as definições de expiração do grupo Office 365, mas requer o ID da política. Este cmdlet desativa a expiração para os grupos do Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Os seguintes cmdlets podem ser usados para configurar a política mais detalhadamente. Para mais informações, consulte a [documentação do PowerShell.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)

- Get-AzureADMSGroupLifecyclePolicy
- Política de ciclode vida do New-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remover-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remover-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos AD Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
