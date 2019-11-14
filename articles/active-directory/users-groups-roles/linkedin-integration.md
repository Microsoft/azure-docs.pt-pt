---
title: Consentimento do administrador para conexões de conta do LinkedIn – Azure AD | Microsoft Docs
description: Explica como habilitar ou desabilitar conexões de conta de integração do LinkedIn em aplicativos da Microsoft no Azure Active Directory
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
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025401"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrar conexões de conta do LinkedIn no Azure Active Directory

Você pode permitir que os usuários em sua organização acessem suas conexões do LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários consentim se conectarem às suas contas. Você pode integrar sua organização no [centro de administração](https://aad.portal.azure.com)do Azure Active Directory (AD do Azure).

> [!IMPORTANT]
> Atualmente, a configuração de conexões da conta do LinkedIn está sendo distribuída para as organizações do Azure AD. Quando ela é distribuída para sua organização, ela é habilitada por padrão.
> 
> Exceção
> * A configuração não está disponível para clientes que usam Microsoft Cloud para o governo dos EUA, Microsoft Cloud Alemanha ou Azure e Office 365 operados pela 21Vianet na China.
> * A configuração está desativada por padrão para locatários provisionados na Alemanha. Observe que a configuração não está disponível para clientes que usam Microsoft Cloud Alemanha.
> * A configuração está desativada por padrão para locatários provisionados na França.
>
> Depois que as conexões de conta do LinkedIn estiverem habilitadas para sua organização, as conexões de conta funcionarão depois que os usuários consentirem os aplicativos que acessam os dados da empresa Para obter informações sobre a configuração de consentimento do usuário, consulte [como remover o acesso de um usuário a um aplicativo](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Habilitar conexões de conta do LinkedIn no portal do Azure

Você pode habilitar as conexões de conta do LinkedIn somente para os usuários aos quais você deseja ter acesso, de toda a organização para apenas os usuários selecionados em sua organização.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com/) com uma conta que seja um administrador global da organização do Azure AD.
1. Selecione **usuários**.
1. Na folha **usuários** , selecione **configurações de usuário**.
1. Em **conexões de conta do LinkedIn**, permita que os usuários conectem suas contas para acessar suas conexões do LinkedIn em alguns aplicativos da Microsoft. Nenhum dado é compartilhado até que os usuários consentim se conectarem às suas contas.

    * Selecione **Sim** para habilitar o serviço para todos os usuários em sua organização
    * Selecione o **grupo selecionado** para habilitar o serviço apenas para um grupo de usuários selecionados em sua organização
    * Selecione **não** para retirar o consentimento de todos os usuários em sua organização

    ![Integrar conexões de conta do LinkedIn na organização](./media/linkedin-integration/linkedin-integration.png)

1. Quando terminar, selecione **salvar** para salvar as configurações.

> [!Important]
> A integração do LinkedIn não está totalmente habilitada para seus usuários até que eles consentissem conectar suas contas. Nenhum dado é compartilhado quando você habilita conexões de conta para seus usuários.

### <a name="assign-selected-users-with-a-group"></a>Atribuir usuários selecionados a um grupo
Substituímos a opção ' Selected ' que especifica uma lista de usuários com a opção de selecionar um grupo de usuários para que você possa habilitar a capacidade de conectar o LinkedIn e contas da Microsoft para um único grupo em vez de muitos usuários individuais. Se você não tiver conexões de conta do LinkedIn habilitadas para usuários individuais selecionados, você não precisará fazer nada. Se você tiver habilitado anteriormente as conexões de conta do LinkedIn para usuários individuais selecionados, deverá:

1. Obter a lista atual de usuários individuais
1. Mover os usuários individuais atualmente habilitados para um grupo
1. Use o grupo do anterior como o grupo selecionado na configuração conexões de conta do LinkedIn no centro de administração do Azure AD.

> [!NOTE]
> Mesmo que você não mova os usuários individuais atualmente selecionados para um grupo, eles ainda poderão ver informações do LinkedIn nos aplicativos da Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Obter a lista atual de usuários selecionados

1. Entre Microsoft 365 com sua conta de administrador.
1. Aceda a https://linkedinselectedusermigration.azurewebsites.net/. Você verá uma lista de usuários que estão selecionados para conexões de conta do LinkedIn.
1. Exporte a lista para um arquivo CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Mover os usuários individuais atualmente selecionados para um grupo

1. Iniciar o PowerShell
1. Instalar o módulo do Azure AD executando `Install-Module AzureAD`
1. Execute o seguintes script:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Para usar o grupo da etapa dois como o grupo selecionado na configuração de conexões da conta do LinkedIn no centro de administração do Azure AD, consulte [habilitar conexões de conta do LinkedIn no portal do Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Usar Política de Grupo para habilitar conexões de conta do LinkedIn

1. Baixar os [arquivos de modelo administrativo do Office 2016 (admx/adml)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extraia os arquivos **ADMX** e copie-os para o armazenamento central.
1. Abra o gerenciamento de Política de Grupo.
1. Crie um objeto de Política de Grupo com a seguinte configuração: **usuário configuração** > **Modelos Administrativos** > **Microsoft Office 2016** > **diversos** > **Mostrar recursos do LinkedIn em aplicativos do Office**.
1. Selecione **habilitado** ou **desabilitado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está habilitada. Os usuários em sua organização podem usar os recursos do LinkedIn em seus aplicativos do Office 2016.
   **Desativado** | A configuração **Mostrar recursos do LinkedIn em aplicativos do Office** nas opções do Office 2016 está desabilitada e os usuários finais não podem alterar essa configuração. Os usuários em sua organização não podem usar recursos do LinkedIn em seus aplicativos do Office 2016.

Essa política de grupo afeta apenas os aplicativos do Office 2016 para um computador local. Se os usuários desabilitarem o LinkedIn em seus aplicativos do Office 2016, eles ainda poderão ver os recursos do LinkedIn no Office 365.

## <a name="next-steps"></a>Passos seguintes

* [Consentimento do usuário e compartilhamento de dados para o LinkedIn](linkedin-user-consent.md)

* [Informações e recursos do LinkedIn em seus aplicativos da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Exiba sua configuração atual de integração do LinkedIn no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
