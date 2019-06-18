---
title: Consentimento de administrador para ligações de conta do LinkedIn - Azure Active Directory | Documentos da Microsoft
description: Explica como ativar ou desativar ligações de conta de integração do LinkedIn nas aplicações da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64920242"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar o LinkedIn ligações de conta no Azure Active Directory

Pode permitir que os utilizadores na sua organização para aceder às suas ligações do LinkedIn dentro de algumas aplicações da Microsoft. Não existem dados são partilhados até que os utilizadores dar consentimento para ligar as suas contas. Pode integrar a sua organização no Azure Active Directory (Azure AD) [Centro de administração](https://aad.portal.azure.com).

> [!IMPORTANT]
> As ligações de conta do LinkedIn definição é implementado para organizações do Azure AD. Quando implementar a sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A definição não está disponível para clientes que utilizam o Microsoft Cloud para administração pública dos EUA, o Microsoft Cloud Alemanha, ou o Azure e o Office 365, operado pela 21Vianet na China.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na Alemanha. Tenha em atenção que a definição não está disponível para clientes que utilizam o Microsoft Cloud Alemanha.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na França.
>
> Depois de ativar as ligações de conta do LinkedIn para a sua organização, as ligações de conta funcionam depois dos utilizadores dar consentimento a aplicações acedam aos dados da empresa em nome deles. Para obter informações sobre a definição de consentimento do utilizador, consulte [como remover o acesso de um utilizador a uma aplicação](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Ativar as ligações de conta do LinkedIn no portal do Azure

Pode ativar ligações de conta do LinkedIn para apenas os utilizadores que pretende ter acesso, a partir de toda a sua organização para apenas os utilizadores selecionados na sua organização.

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com/) com uma conta que seja um administrador global para a organização do Azure AD.
1. Selecione **utilizadores**.
1. Sobre o **usuários** painel, selecione **as definições de utilizador**.
1. Sob **ligações de conta do LinkedIn**, permitir que os utilizadores liguem as contas para aceder às suas ligações do LinkedIn dentro de algumas aplicações da Microsoft. Não existem dados são partilhados até que os utilizadores dar consentimento para ligar as suas contas.

    * Selecione **Sim** para ativar o serviço para todos os utilizadores na sua organização
    * Selecione **selecionados** para permitir que apenas um grupo de utilizadores selecionados na sua organização
    * Selecione **não** para retirar o consentimento de todos os utilizadores na sua organização

    ![Integrar o LinkedIn ligações de conta da organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando tiver terminado, selecione **guardar** para guardar as definições.

> [!Important]
> Integração do LinkedIn não ficar totalmente ativada para os seus utilizadores até que eles o consentimento para ligar as suas contas. Nenhum dado é compartilhado quando ativa a ligações de conta para os seus utilizadores.

### <a name="assign-selected-users-with-a-group"></a>Atribuir utilizadores selecionados com um grupo
Podemos ter substituído a opção de "Selecionado" que especifica uma lista de utilizadores com a opção de selecionar um grupo de utilizadores para que pode habilitar a capacidade de ligar as contas do LinkedIn e da Microsoft para um único grupo em vez do número de utilizadores individual. Se não tiver as ligações de conta do LinkedIn ativadas para utilizadores individuais selecionados, não precisa de fazer nada. Se ativou anteriormente as ligações de conta do LinkedIn para utilizadores individuais selecionados, deve:

1. Obter a lista atual de utilizadores individuais
1. Mover os utilizadores individuais atualmente ativados para um grupo
1. Utilize o grupo do anterior como o grupo selecionado nas ligações de conta LinkedIn definição no Centro de administração do Azure AD.

> [!NOTE]
> Mesmo que não mover os utilizadores individuais atualmente selecionados para um grupo, ainda pode ver informações do LinkedIn nas aplicações da Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Obter a lista atual dos utilizadores selecionados

1. Inicie sessão no Microsoft 365 com a sua conta de administrador.
1. Aceda a https://linkedinselectedusermigration.azurewebsites.net/. Verá a lista de utilizadores que estão selecionados para ligações de conta do LinkedIn.
1. Exporte a lista para um ficheiro CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Mover os utilizadores individuais atualmente selecionados para um grupo

1. Iniciar o PowerShell
1. Instalar o módulo Azure AD através da execução `Install-Module AzureAD`
1. Execute o seguintes script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para utilizar o grupo do passo dois do grupo selecionado nas ligações de conta LinkedIn definição no Centro de administração do Azure AD, veja [LinkedIn ativar ligações de conta no portal do Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilizar a política de grupo para ativar as ligações de conta do LinkedIn

1. Transferir o [arquivos de modelo de administração do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrair os **ADMX** arquivos e copie-os para o armazenamento central.
1. Gerenciamento de diretiva de grupo de Open.
1. Crie um objeto de política de grupo com a definição seguinte: **Configuração do usuário** > **modelos administrativos** > **Microsoft Office 2016** > **diversas**  >  **Funcionalidades do LinkedIn Mostrar em aplicativos do Office**.
1. Selecione **habilitado** ou **desativado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** nas opções do Office 2016 está habilitada. Os utilizadores na sua organização podem utilizar funcionalidades do LinkedIn em seus aplicativos do Office 2016.
   **Desativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** definição nas opções do Office 2016 está desativada e os utilizadores finais não é possível alterar esta definição. Os utilizadores na sua organização não é possível utilizar funcionalidades do LinkedIn em seus aplicativos do Office 2016.

Esta política de grupo afeta apenas as aplicações do Office 2016 para um computador local. Se os utilizadores desativarem LinkedIn nas suas aplicações do Office 2016, ainda pode ver funcionalidades do LinkedIn no Office 365.

## <a name="next-steps"></a>Passos Seguintes

* [Consentimento do utilizador e dados de partilha para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e funcionalidades nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Ver a definição de integração do LinkedIn atual no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
