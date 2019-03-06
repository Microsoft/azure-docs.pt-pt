---
title: Definir expiração para grupos do Office 365 - Azure Active Directory | Documentos da Microsoft
description: Como configurar a expiração de grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e227744864ee7204e29a029b4a479fc2d9e2f3c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446389"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurar a política de expiração para grupos do Office 365

Agora pode gerenciar o ciclo de vida de grupos do Office 365 ao definir uma política de expiração para eles. Pode definir a política de expiração para apenas os grupos do Office 365 no Azure Active Directory (Azure AD). 

Depois de definir um grupo a expirar:
-   Os proprietários do grupo são notificados para renovar o grupo que está prestes à expiração
-   Qualquer grupo que não for renovado é eliminado
-   Qualquer grupo do Office 365, que é eliminado pode ser restaurado dentro de 30 dias, os proprietários do grupo ou o administrador

Atualmente, apenas uma política de expiração pode ser configurada para grupos do Office 365 num inquilino.

> [!NOTE]
> Configurar e utilizar a política de expiração para grupos do Office 365 requerem às suas mãos licenças do Azure AD Premium para todos os membros dos grupos aos quais é aplicada a política de expiração.

Para obter informações sobre como transferir e instalar os cmdlets do PowerShell do Azure AD, consulte [Azure Active Directory PowerShell para Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Funções e permissões
Seguem-se as funções que podem configurar e utilizar a expiração de grupos do Office 365 no Azure AD.

Função | Permissões
-------- | --------
Administrador global ou administrador de conta de utilizador | Pode criar, ler, atualizar ou eliminar as definições de política de expiração de grupos do Office 365<br>Pode renovar a qualquer grupo do Office 365
Utilizador | Pode renovar um grupo do Office 365 que possuem<br>Pode restaurar um grupo do Office 365 que possuem<br>Pode ler as definições de política de expiração

Para obter mais informações sobre as permissões para restaurar um grupo eliminado, consulte [restaurar um grupo eliminado do Office 365 no Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração do grupo

1. Abra o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global no inquilino do Azure AD.

2. Selecione **grupos**, em seguida, selecione **expiração** para abrir as definições de expiração.
  
  ![Painel de expiração](./media/groups-lifecycle/expiration-settings.png)

3. Sobre o **expiração** painel, pode:

  * Defina a duração do grupo em dias. Pode selecionar um dos valores predefinidos ou um valor personalizado (deve ser 31 dias ou mais). 
  * Especifique um endereço de e-mail em que as notificações de renovação e de expiração devem ser enviadas quando um grupo não tem proprietários. 
  * Selecione os grupos do Office 365 expirarem. Pode ativar a expiração para **todos os** grupos do Office 365, pode optar por ativar apenas **selecionados** grupos do Office 365, ou selecione **None**  para desativar a expiração de todos os grupos.
  * Guardar as definições, quando tiver terminado, selecionando **guardar**.

> [!NOTE]
> * Quando configurar a expiração pela primeira vez, todos os grupos que são mais antigos do que o intervalo de expiração estão definidos para 30 dias até à expiração. O primeiro e-mail de notificação de renovação é enviado no prazo de um dia. Por exemplo, um grupo foi criado de 400 dias atrás, e o intervalo de expiração é definido para 180 dias. Quando aplicar definições de expiração, o grupo A tem 30 dias antes de ser eliminado, a menos que o proprietário renova-lo.
> * Quando um grupo dinâmico é eliminado e restaurado, é visto como um novo grupo e novamente preenchido de acordo com a regra. Este processo pode demorar até 24 horas.

## <a name="email-notifications"></a>Notificações por e-mail

Notificações de e-mail como este são enviadas para os proprietários do grupo do Office 365 30 dias, 15 dias e 1 dia antes da expiração do grupo. O idioma da mensagem de e-mail é determinado pelo idioma preferencial do proprietário de grupos ou a linguagem de inquilino. Se o proprietário do grupo tiver definido um idioma preferencial ou os proprietários de vários têm o mesmo idioma preferencial, é utilizado esse idioma. Para todos os outros casos, é utilizado o idioma de inquilino.

![Notificação de e-mail de expiração](./media/groups-lifecycle/expiration-notification.png)

Partir do **renovar grupo** e-mail de notificação, diretamente o grupo proprietários podem acessar a página de detalhes do grupo no painel de acesso. Lá, os utilizadores podem obter mais informações sobre o grupo, como a respetiva descrição, quando ele foi última renovação, quando irá expirar e também a capacidade de renovar o grupo. A página de detalhes do grupo agora também inclui ligações para recursos de grupo do Office 365, para que o proprietário do grupo convenientemente pode ver o conteúdo e a atividade no respetivo grupo.

Quando um grupo de expira, o grupo é eliminado um dia após a data de expiração. É enviada uma notificação de e-mail como este para os proprietários de grupo do Office 365, informar sobre a expiração e a eliminação subsequente do respetivo grupo do Office 365.

![Notificação de e-mail de eliminação do grupo](./media/groups-lifecycle/deletion-notification.png)

O grupo pode ser restaurado dentro de 30 dias após a sua eliminação selecionando **grupo de restauro** ou utilizando os cmdlets do PowerShell, conforme descrito na [restaurar um grupo eliminado do Office 365 no Azure Active Directory](groups-restore-deleted.md).
    
Se o grupo de que está a restaurar contém documentos, sites do SharePoint ou outros objetos persistentes, poderá demorar até 24 horas para restaurar completamente o grupo e o respetivo conteúdo.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Como obter a data de expiração de grupo do Office 365
Para além do painel de acesso, onde os utilizadores podem ver os detalhes de grupo, incluindo a data de expiração e data da última renovada, data de expiração de um grupo do Office 365 pode ser obtida da versão Beta do Microsoft Graph REST API. foi ativado expirationDateTime como uma propriedade de grupo na versão Beta do Microsoft Graph. Pode ser obtida com um pedido GET. Para obter mais detalhes, consulte [neste exemplo](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Para gerir membros do grupo no painel de acesso, "Restringir o acesso a grupos no painel de acesso" tem de ser definido como "Não", no Azure Active Directory grupos de definições gerais.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como funciona o expiração de grupo do Office 365 com uma caixa de correio em retenção legal
Quando um grupo expira e é eliminado, em seguida, de 30 dias após a eliminação de dados do grupo de aplicações, Planner, Sites, como ou Equipes é eliminado permanentemente, mas a caixa de correio de grupo que está em suspensão legal é mantida e não é eliminada permanentemente. O administrador pode utilizar os cmdlets do Exchange para restaurar a caixa de correio para obter os dados. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Como funciona a expiração de grupo do Office 365 com a política de retenção
A política de retenção está configurada por meio do Centro de conformidade e segurança. Se tiver definido uma política de retenção para os grupos do Office 365, quando um grupo expira e é eliminado, as conversações de grupo na caixa de correio de grupo e ficheiros no site de grupo são retidos no contentor de retenção para o número de dias definido no período de retenção específico política. Os utilizadores não verão o grupo ou o seu conteúdo após a expiração, mas podem recuperar os dados de site e a caixa de correio por meio de deteção eletrónica.

## <a name="powershell-examples"></a>Exemplos do PowerShell
Seguem-se exemplos de como pode utilizar os cmdlets do PowerShell para configurar as definições de expiração para grupos do Office 365 no seu inquilino:

1. Instalar o módulo de pré-visualização do PowerShell versão 2.0 (2.0.0.137) e inicie sessão na linha de comandos do PowerShell:
  ```
  Install-Module -Name AzureADPreview
  connect-azuread 
  ```
2. Configure as definições de expiração New-AzureADMSGroupLifecyclePolicy:  Este cmdlet define o tempo de vida para todos os grupos do Office 365 no inquilino para 365 dias. Notificações de renovação para o Office 365 grupos sem proprietários serão enviados para "emailaddress@contoso.com'
  
  ```
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ```
3. Obter a política existente Get-AzureADMSGroupLifecyclePolicy: Este cmdlet obtém as definições de expiração grupo atuais do Office 365 foram configuradas. Neste exemplo, pode ver:
  * O ID de política 
  * O tempo de vida para todos os grupos do Office 365 no inquilino está definido como 365 dias
  * Notificações de renovação para o Office 365 grupos sem proprietários serão enviados para "emailaddress@contoso.com."
  
  ```
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ``` 
   
4. Atualize a política existente conjunto-AzureADMSGroupLifecyclePolicy: Este cmdlet é utilizado para atualizar uma política existente. O exemplo abaixo, o tempo de vida do grupo na política existente é alterado de 365 dias para 180 dias. 
  
  ```
  Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ```
  
5. Adicione grupos específicos para a política de Add-AzureADMSLifecyclePolicyGroup: Este cmdlet adiciona um grupo para a política de ciclo de vida. Por exemplo: 
  
  ```
  Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ```
  
6. Remova Remove-AzureADMSGroupLifecyclePolicy de política existente: Este cmdlet elimina as definições de expiração de grupo do Office 365, mas requer o ID de política. Este procedimento desativará a expiração de grupos do Office 365. 
  
  ```
  Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
  ```
  
Os seguintes cmdlets pode ser utilizados para configurar a política mais detalhadamente. Para obter mais informações, consulte [documentação do PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passos Seguintes
Estes artigos fornecem informações adicionais sobre os grupos do Azure AD.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
