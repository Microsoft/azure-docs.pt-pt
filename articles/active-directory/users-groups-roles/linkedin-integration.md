---
title: Consentimento do Administrador para ligações à conta LinkedIn - Azure AD / Microsoft Docs
description: Explica como ativar ou desativar as ligações de conta de integração do LinkedIn em aplicações da Microsoft no Diretório Ativo do Azure
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa80bf09113786e35a6ce98157f18639e30d210
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582640"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar as ligações de conta linkedIn no Diretório Ativo do Azure

Pode permitir que os utilizadores da sua organização acedam às suas ligações LinkedIn dentro de algumas aplicações da Microsoft. Não são partilhados dados até que os utilizadores consintam em ligar as suas contas. Pode integrar a sua organização no centro de [administração](https://aad.portal.azure.com)azure Ative (Azure AD).

> [!IMPORTANT]
> A definição de ligações à conta LinkedIn está atualmente a ser distribuída para as organizações da AD Azure. Quando é lançado para a sua organização, é ativado por padrão.
>
> Exceções:
>
> * A definição não está disponível para clientes que utilizem o Microsoft Cloud para o Governo dos EUA, Microsoft Cloud Germany ou Azure e Office 365 operados pela 21Vianet na China.
> * A definição é desligada por defeito para as organizações Azure AD aprovisionadas na Alemanha. Note que a definição não está disponível para clientes que usam o Microsoft Cloud Germany.
> * A definição é desligada por defeito para as organizações aprovisionadas em França.
>
> Assim que as ligações de conta do LinkedIn estiverem ativadas para a sua organização, as ligações da conta funcionam após os utilizadores consentirem que as aplicações acedem aos dados da empresa em seu nome. Para obter informações sobre a definição de consentimento do utilizador, consulte como remover o [acesso de um utilizador a uma aplicação](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Ativar as ligações da conta LinkedIn no portal Azure

Pode ativar as ligações de conta do LinkedIn apenas para os utilizadores que pretende ter acesso, desde toda a sua organização até apenas utilizadores selecionados na sua organização.

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com/) com uma conta que é uma administração global para a organização Azure AD.
1. Selecione **Utilizadores**.
1. Na página **Utilizadores,** selecione **as definições do Utilizador**.
1. No âmbito das ligações da **conta LinkedIn,** permite que os utilizadores conectem as suas contas para acederem às suas ligações LinkedIn dentro de algumas aplicações da Microsoft. Não são partilhados dados até que os utilizadores consintam em ligar as suas contas.

    * Selecione **Sim** para ativar o serviço para todos os utilizadores da sua organização
    * Selecione **Grupo Selecionado** para ativar o serviço apenas para um grupo de utilizadores selecionados na sua organização
    * Selecione **Não** para retirar o consentimento de todos os utilizadores da sua organização

    ![Integrar as ligações de conta linkedIn na organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, selecione **Guardar** para guardar as definições.

> [!Important]
> A integração do LinkedIn não está totalmente ativada para os seus utilizadores até que consintam em ligar as suas contas. Não são partilhados dados quando permite as ligações de conta para os seus utilizadores.

### <a name="assign-selected-users-with-a-group"></a>Atribuir utilizadores selecionados com um grupo

Substituímos a opção 'Selected' que especifica uma lista de utilizadores com a opção de selecionar um grupo de utilizadores para que possa permitir a capacidade de ligar as contas do LinkedIn e da Microsoft a um único grupo em vez de muitos utilizadores individuais. Se não tiver ligações de conta LinkedIn ativadas para utilizadores individuais selecionados, não precisa de fazer nada. Se já tiver ativado previamente as ligações de conta linkedIn para utilizadores individuais selecionados, deverá:

1. Obtenha a lista atual de utilizadores individuais
1. Mover os utilizadores individuais atualmente habilitados para um grupo
1. Utilize o grupo do anterior como o grupo selecionado na definição de conexões de conta LinkedIn no centro de administração Azure AD.

> [!NOTE]
> Mesmo que não mude os utilizadores individuais atualmente selecionados para um grupo, ainda podem ver informações do LinkedIn nas aplicações da Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Mova os utilizadores atualmente selecionados para um grupo

1. Crie um ficheiro CSV dos utilizadores selecionados para ligações à conta LinkedIn.
1. Assine o Microsoft 365 com a sua conta de administrador.
1. Inicie o PowerShell.
1. Instale o módulo Azure AD em execução`Install-Module AzureAD`
1. Execute o seguintes script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para utilizar o grupo desde o segundo passo como o grupo selecionado na definição de conexões de conta LinkedIn no centro de administração Azure AD, consulte as ligações da [conta Enable LinkedIn no portal Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilize a Política de Grupo para permitir ligações à conta LinkedIn

1. Descarregue os ficheiros de [modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraios os ficheiros **ADMX** e copie-os para a sua loja central.
1. Abra a Gestão de Políticas de Grupo.
1. Crie um Objeto de Política de Grupo com a seguinte definição:**Modelos administrativos** > de **configuração** > do**utilizador Microsoft Office 2016** > Funcionalidades > de LinkedIn de série**miscelânea****nas aplicações do Office**.
1. Selecione **Ativado** ou **Desativado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | O **Show LinkedIn apresenta-se na** definição de aplicações do Office 2016 Options está ativado. Os utilizadores da sua organização podem utilizar funcionalidades do LinkedIn nas suas aplicações do Office 2016.
   **Desativado** | As funcionalidades do Show LinkedIn na definição de **aplicações do Office** 2016 Options is disabled and end users can't change this setting. Os utilizadores da sua organização não podem utilizar funcionalidades do LinkedIn nas suas aplicações do Office 2016.

Esta política de grupo afeta apenas aplicações do Office 2016 para um computador local. Se os utilizadores desativarem o LinkedIn nas suas aplicações do Office 2016, ainda podem ver funcionalidades do LinkedIn no Office 365.

## <a name="next-steps"></a>Passos seguintes

* [Consentimento do utilizador e partilha de dados para o LinkedIn](linkedin-user-consent.md)

* [Informações e funcionalidades do LinkedIn nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda LinkedIn](https://www.linkedin.com/help/linkedin)

* [Veja o seu atual cenário de integração linkedIn no portal Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
