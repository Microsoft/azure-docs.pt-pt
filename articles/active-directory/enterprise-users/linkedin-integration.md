---
title: Consentimento de administração para ligações de conta LinkedIn - Azure AD | Microsoft Docs
description: Explica como ativar ou desativar as ligações de conta de integração do LinkedIn em aplicações da Microsoft no Azure Ative Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ffa937f91af7078b28331a655f99397d3821def
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545898"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar ligações de conta LinkedIn no Azure Ative Directory

Pode permitir que os utilizadores da sua organização acedam às suas ligações LinkedIn dentro de algumas aplicações da Microsoft. Nenhum dado é partilhado até que os utilizadores consintam em ligar as suas contas. Pode integrar a sua organização no [centro de administração](https://aad.portal.azure.com)Azure Ative (Azure AD).

> [!IMPORTANT]
> A definição de ligações à conta LinkedIn está atualmente a ser lançada para organizações Azure AD. Quando é lançado para a sua organização, é ativado por padrão.
>
> Exceções:
>
> * A definição não está disponível para clientes que utilizem o Microsoft Cloud para governo dos EUA, Microsoft Cloud Germany ou Azure e Microsoft 365 operados por 21Vianet na China.
> * A definição é cancelada por defeito para as organizações AZure AD a provisionadas na Alemanha. Note que a definição não está disponível para clientes que usam o Microsoft Cloud Germany.
> * A definição é por defeito para as organizações a provisionadas em França.
>
> Uma vez que as ligações de conta LinkedIn são ativadas para a sua organização, as ligações de conta funcionam após o consentimento dos utilizadores para aplicações que acedem aos dados da empresa em seu nome. Para obter informações sobre a definição de consentimento do utilizador, consulte [Como remover o acesso de um utilizador a uma aplicação](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Ativar as ligações de conta LinkedIn no portal Azure

Pode ativar as ligações de conta LinkedIn apenas para os utilizadores a que pretende ter acesso, desde toda a sua organização até utilizadores selecionados na sua organização.

1. Inscreva-se no centro de administração da [Azure Com](https://aad.portal.azure.com/) uma conta que é um administrador global para a organização Azure AD.
1. Selecione **Utilizadores**.
1. Na página **do Utilizador,** selecione **as definições do Utilizador.**
1. Nas **ligações à conta do LinkedIn,** os utilizadores permitem que os utilizadores conectem as suas contas para acederem às suas ligações LinkedIn dentro de algumas aplicações da Microsoft. Nenhum dado é partilhado até que os utilizadores consintam em ligar as suas contas.

    * Selecione **Sim** para ativar o serviço para todos os utilizadores da sua organização
    * Selecione **grupo selecionado** para ativar o serviço para apenas um grupo de utilizadores selecionados na sua organização
    * Selecione **Não** para retirar o consentimento de todos os utilizadores da sua organização

    ![Integrar ligações de conta LinkedIn na organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, **selecione Guardar** para guardar as suas definições.

> [!Important]
> A integração do LinkedIn não está totalmente ativada para os seus utilizadores até que eles consintam em ligar as suas contas. Nenhum dado é partilhado quando ativa as ligações de conta para os seus utilizadores.

### <a name="assign-selected-users-with-a-group"></a>Atribuir utilizadores selecionados com um grupo

Substituímos a opção 'Seleccionado' que especifica uma lista de utilizadores com a opção de selecionar um grupo de utilizadores para que possa ativar a capacidade de ligar as contas do LinkedIn e microsoft para um único grupo em vez de muitos utilizadores individuais. Se não tiver ligações de conta LinkedIn ativadas para utilizadores individuais selecionados, não precisa de fazer nada. Se já ativou ligações de conta LinkedIn para utilizadores individuais selecionados, deve:

1. Obtenha a lista atual de utilizadores individuais
1. Mover os utilizadores individuais atualmente habilitados para um grupo
1. Utilize o grupo do anterior como grupo selecionado nas ligações de conta LinkedIn no centro de administração Admin Azure.

> [!NOTE]
> Mesmo que não mova os utilizadores individuais atualmente selecionados para um grupo, eles ainda podem ver informações do LinkedIn nas aplicações da Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Mover utilizadores atualmente selecionados para um grupo

1. Crie um ficheiro CSV dos utilizadores selecionados para ligações de conta LinkedIn.
1. Inscreva-se no Microsoft 365 com a sua conta de administrador.
1. Inicie o PowerShell.
1. Instale o módulo AD Azure em execução `Install-Module AzureAD`
1. Execute o seguintes script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para utilizar o grupo a partir do segundo passo como grupo selecionado nas ligações de conta LinkedIn no centro de administração AD Azure, consulte [ativar as ligações da conta LinkedIn no portal Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilizar a Política de Grupo para permitir ligações de conta LinkedIn

1. Descarregue os [ficheiros de modelo administrativo do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraia os ficheiros **ADMX** e copie-os para a sua loja central.
1. Abra a Gestão de Políticas de Grupo.
1. Criar um objeto de política de grupo com a seguinte definição: Modelos administrativos **de configuração** do utilizador  >    >  **Microsoft Office 2016**  >  **Funcionalidades do**  >  **LinkedIn em aplicações do Office**.
1. Selecione **Ativado** ou **Desativado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | O **Show LinkedIn apresenta-se nas aplicações do Office** a definição de opções do Office 2016. Os utilizadores da sua organização podem utilizar funcionalidades do LinkedIn nas suas aplicações Office 2016.
   **Desativado** | As **funcionalidades do Show LinkedIn na** definição de aplicações do Office 2016 São desativadas e os utilizadores finais não podem alterar esta definição. Os utilizadores da sua organização não podem utilizar funcionalidades do LinkedIn nas suas aplicações Office 2016.

Esta política de grupo afeta apenas as aplicações do Office 2016 para um computador local. Se os utilizadores desativarem o LinkedIn nas suas aplicações office 2016, ainda podem ver as funcionalidades do LinkedIn no Microsoft 365.

## <a name="next-steps"></a>Passos seguintes

* [Consentimento do utilizador e partilha de dados para o LinkedIn](linkedin-user-consent.md)

* [Informações e funcionalidades do LinkedIn nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda LinkedIn](https://www.linkedin.com/help/linkedin)

* [Veja a sua configuração atual de integração do LinkedIn no portal Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)