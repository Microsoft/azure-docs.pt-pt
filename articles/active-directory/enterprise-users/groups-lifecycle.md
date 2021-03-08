---
title: Desativar a expiração para os grupos Microsoft 365 - Azure Ative Directory | Microsoft Docs
description: Como configurar a expiração para os grupos Microsoft 365 no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a2a69b5013ab8e1bc4637f21ce484a132d37834
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455717"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Configure a política de expiração para os grupos Microsoft 365

Este artigo diz-lhe como gerir o ciclo de vida dos grupos Microsoft 365, definindo uma política de expiração para eles. Só pode definir a política de expiração para os grupos Microsoft 365 no Azure Ative Directory (Azure AD).

Uma vez que definir um grupo para expirar:

- Os grupos com atividades de utilizador são automaticamente renovados à medida que a expiração se aproxima.
- Os proprietários do grupo são notificados para renovar o grupo, caso o grupo não seja renovado automaticamente.
- Qualquer grupo que não seja renovado é eliminado.
- Qualquer grupo Microsoft 365 que seja eliminado pode ser restaurado no prazo de 30 dias pelos proprietários do grupo ou pelo administrador.

Atualmente, apenas uma política de expiração pode ser configurada para todos os grupos Microsoft 365 numa organização AD AZure.

> [!NOTE]
> Configurar e utilizar a política de expiração para os grupos Microsoft 365 requer que possua, mas não necessariamente, atribua licenças Azure AD Premium aos membros de todos os grupos aos quais a política de expiração é aplicada.

Para obter informações sobre como descarregar e instalar os cmdlets Azure AD PowerShell, consulte [o Azure Ative Directory PowerShell para obter o gráfico 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Renovação automática baseada na atividade

Com a inteligência AD AZure, os grupos são agora automaticamente renovados com base no facto de terem sido usados recentemente. Esta funcionalidade elimina a necessidade de ação manual por parte dos proprietários do grupo, uma vez que se baseia na atividade do utilizador em grupos em todos os serviços da Microsoft 365, como Outlook, SharePoint ou Teams. Por exemplo, se um proprietário ou membro do grupo fizer algo como carregar um documento no SharePoint, visitar um canal de Equipas ou enviar um e-mail para o grupo no Outlook, o grupo é automaticamente renovado cerca de 35 dias antes do fim do grupo e o proprietário não recebe notificações de renovação.

### <a name="activities-that-automatically-renew-group-expiration"></a>Atividades que renovam automaticamente a expiração do grupo

As seguintes ações de utilizador causam a renovação automática do grupo:

- SharePoint: Ver, editar, transferir, mover, partilhar ou carregar ficheiros
- Outlook: Junte-se ao grupo, leia/escreva mensagem de grupo a partir do espaço de grupo, Como uma mensagem (no Outlook Web Access)
- Equipas: Visite um canal de equipas

### <a name="auditing-and-reporting"></a>Auditoria e reporte

Os administradores podem obter uma lista de grupos renovados automaticamente a partir dos registos de auditoria de atividade em Azure AD.

![Renovação automática de grupos com base na atividade](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Seguem-se as funções que podem configurar e utilizar a expiração para os grupos Microsoft 365 em Azure AD.

Função | Permissões
-------- | --------
Administrador global, administrador do grupo ou administrador de utilizadores | Pode criar, ler, atualizar ou eliminar as definições de política de expiração dos grupos Microsoft 365<br>Pode renovar qualquer grupo Microsoft 365
User | Pode renovar um grupo Microsoft 365 que possuem<br>Pode restaurar um grupo Microsoft 365 que possuem<br>Pode ler as definições da política de expiração

Para obter mais informações sobre permissões para restaurar um grupo eliminado, consulte [Restaurar um grupo Microsoft 365 eliminado no Azure Ative Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Abra o [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que é um administrador global na sua organização Azure AD.

2. Selecione **Grupos** e, em seguida, **selecione Expiração** para abrir as definições de expiração.
  
   ![Definições de expiração para grupos](./media/groups-lifecycle/expiration-settings.png)

3. Na página **expiração,** pode:

    - Desembarate a vida útil do grupo em dias. Pode selecionar um dos valores predefinidos ou um valor personalizado (deve ser de 30 dias ou mais).
    - Especifique um endereço de e-mail onde as notificações de renovação e expiração devem ser enviadas quando um grupo não tem proprietário.
    - Selecione quais os grupos Microsoft 365 que expiram. Pode definir a expiração para:
      - **Todos os** Microsoft 365 grupos
      - Uma lista de **grupos selecionados** da Microsoft 365
      - **Nenhum** para restringir a expiração para todos os grupos
    - Guarde as suas definições quando terminar selecionando **Guardar**.

> [!NOTE]
> - Quando configurar a expiração pela primeira vez, quaisquer grupos mais velhos do que o intervalo de validade são definidos para 35 dias até ao termo, a menos que o grupo seja renovado automaticamente ou o proprietário o renove.
> - Quando um grupo dinâmico é eliminado e restaurado, é visto como um novo grupo e realvoado de acordo com a regra. Este processo pode demorar até 24 horas.
> - Os avisos de expiração para grupos utilizados nas Equipas aparecem no feed dos Proprietários das Equipas.

## <a name="email-notifications"></a>Notificações por e-mail

Se os grupos não forem automaticamente renovados, notificações por e-mail como esta são enviadas para os proprietários do grupo Microsoft 30 dias, 15 dias e 1 dia antes da expiração do grupo. A linguagem do e-mail é determinada pela linguagem preferida do proprietário dos grupos ou pela definição de idioma AZure AD. Se o proprietário do grupo definiu uma língua preferida, ou vários proprietários têm a mesma língua preferida, então essa língua é usada. Para todos os outros casos, é utilizada a definição de idioma AZure AD.

![Notificações de e-mail de expiração](./media/groups-lifecycle/expiration-notification.png)

A partir do e-mail de notificação **do grupo Renovar,** os proprietários do grupo podem aceder diretamente à página de detalhes do grupo no [Painel de Acesso.](https://account.activedirectory.windowsazure.com/r#/applications) Lá, os utilizadores podem obter mais informações sobre o grupo, como a sua descrição, quando foi renovado pela última vez, quando expirará, e também a capacidade de renovar o grupo. A página de detalhes do grupo agora também inclui links para os recursos do grupo Microsoft 365, para que o proprietário do grupo possa convenientemente ver o conteúdo e atividade no seu grupo.

Quando um grupo expira, o grupo é eliminado um dia após a data de validade. Uma notificação por e-mail como esta é enviada para os proprietários do grupo Microsoft 365 informando-os sobre a expiração e subsequente eliminação do seu grupo Microsoft 365.

![Notificações de email de eliminação de grupo](./media/groups-lifecycle/deletion-notification.png)

O grupo pode ser restaurado no prazo de 30 dias após a sua eliminação selecionando **o grupo Restore** ou utilizando cmdlets PowerShell, conforme descrito no Restore um grupo Microsoft [365 eliminado no Azure Ative Directory](groups-restore-deleted.md). Por favor, note que o período de restauração do grupo de 30 dias não é personalizável.

Se o grupo que está a restaurar contiver documentos, sites SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar totalmente o grupo e o seu conteúdo.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Como recuperar a data de validade do grupo Microsoft 365

Além do Painel de Acesso onde os utilizadores podem ver detalhes do grupo, incluindo a data de validade e a última data renovada, a data de validade de um grupo Microsoft 365 pode ser recuperada a partir da API Beta do Microsoft Graph REST. expiraçãoDateTime como uma propriedade de grupo foi ativado em Microsoft Graph Beta. Pode ser recuperado com um pedido GET. Para mais detalhes, consulte [este exemplo.](/graph/api/group-get?view=graph-rest-beta#example)

> [!NOTE]
> Para gerir os membros do grupo no Painel de Acesso, é necessário definir "Restringir o acesso aos grupos no painel de acesso" para "Não" na Definição Geral dos Grupos de Diretório Ativo do Azure.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como a expiração do grupo Microsoft 365 funciona com uma caixa de correio em espera legal

Quando um grupo expira e é eliminado, então 30 dias após a eliminação os dados do grupo de apps como Planner, Sites ou Teams é permanentemente eliminado, mas a caixa de correio do grupo que está em espera legal é mantida e não é permanentemente eliminada. O administrador pode usar cmdlets de troca para restaurar a caixa de correio para recolher os dados.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Como a expiração do grupo Microsoft 365 funciona com a política de retenção

A política de retenção é configurada através do Centro de Segurança e Conformidade. Se tiver estabelecido uma política de retenção para os grupos Microsoft 365, quando um grupo expira e é eliminado, as conversas de grupo na caixa de correio do grupo e ficheiros no site do grupo são conservadas no recipiente de retenção para o número específico de dias definidos na política de retenção. Os utilizadores não verão o grupo ou o seu conteúdo após a expiração, mas podem recuperar os dados do site e da caixa de correio através da descoberta por e-discovery.

## <a name="powershell-examples"></a>Exemplos powerShell

Aqui estão exemplos de como pode utilizar cmdlets PowerShell para configurar as definições de expiração para os grupos Microsoft 365 na sua organização AZure AD:

1. Instale o módulo PowerShell v2.0 e inscreva-se na indicação PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configure as definições de expiração Utilize o New-AzureADMSGroupLifecyclePolicy cmdlet para definir a vida útil de todos os grupos Microsoft 365 na organização AD Azure para 365 dias. As notificações de renovação para os grupos Microsoft 365 sem proprietários serão enviadas para emailaddress@contoso.com '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperar a política existente Get-AzureADMSGroupLifecyclePolicy: Este cmdlet recupera as atuais definições de expiração do grupo Microsoft 365 que foram configuradas. Neste exemplo, pode ver:

   - A iD da política
   - A vida útil de todos os grupos Microsoft 365 na organização AD AZure está definida para 365 dias
   - As notificações de renovação para os grupos Microsoft 365 sem proprietários serão enviadas para emailaddress@contoso.com '.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Atualize a política existente Set-AzureADMSGroupLifecyclePolicy: Este cmdlet é utilizado para atualizar uma política existente. No exemplo abaixo, a vida útil do grupo na política existente é alterada de 365 dias para 180 dias.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adicione grupos específicos à política Add-AzureADMSLifecyclePolicyGroup: Este cmdlet adiciona um grupo à política do ciclo de vida. Em forma de exemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Remover a política existente Remove-AzureADMSGroupLifecyclePolicy: Este cmdlet elimina as definições de expiração do grupo Microsoft 365, mas requer o ID da política. Este cmdlet desativa a expiração para os grupos Microsoft 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Os cmdlets seguintes podem ser usados para configurar a política com mais detalhes. Para obter mais informações, consulte [a documentação powerShell](/powershell/module/azuread/?view=azureadps-2.0-preview#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos AZURE AD.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
