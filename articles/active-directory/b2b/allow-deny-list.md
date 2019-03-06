---
title: Permitir ou bloquear convites para organizações específicas - Azure Active Directory | Documentos da Microsoft
description: Mostra como um administrador pode utilizar o portal do Azure ou o PowerShell para definir um acesso ou negar lista para permitir ou impedir que os utilizadores B2B determinados domínios.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1121b72bf631cbcee4c8d9502fd8a439c26fbf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440119"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para utilizadores B2B de organizações específicos

Pode utilizar uma lista de permissões ou uma lista de negações para permitir ou bloquear convites aos utilizadores B2B de organizações específicas. Por exemplo, se deseja bloquear domínios de endereço de e-mail pessoal, pode configurar uma lista de negações, que contém domínios, como o Gmail.com e Outlook.com. Ou, se sua empresa tem uma parceria com outras empresas, como Contoso.com e Fabrikam.com e Litware.com, e quiser restringir convites para apenas essas organizações, pode adicionar Contoso.com e Fabrikam.com e Litware.com à sua lista de permissões.
  
## <a name="important-considerations"></a>Considerações importantes

- Pode criar uma lista de permissões ou uma lista de negações. Não é possível configurar ambos os tipos de listas. Por predefinição, os domínios não se encontram na lista de permissões que estão na lista de negações e vice-versa. 
- Pode criar apenas uma política de por organização. Pode atualizar a política para incluir mais domínios ou é possível eliminar a política para criar um novo. 
- Esta lista funciona independentemente do OneDrive para empresas e SharePoint Online listas de permitir/bloquear. Se quiser restringir a partilha no SharePoint Online de ficheiros individuais, terá de configurar um permitir ou negar lista para o OneDrive para empresas e SharePoint Online. Para obter mais informações, consulte [restrito de domínios de partilha no SharePoint Online e OneDrive para empresas](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Esta lista não é aplicável a utilizadores externos que já tem resgatado o convite. A lista será imposta depois de configurar a lista. Se um convite de utilizador está num estado pendente, e define uma política que bloqueia o seu domínio, a tentativa do utilizador para resgatar o convite falhará.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Defina a permitir ou negar a política de lista no portal do

Por predefinição, o **permitir convites ser enviado para qualquer domínio (mais inclusivo)** definição está ativada. Neste caso, pode convidar utilizadores B2B de qualquer organização.

### <a name="add-a-deny-list"></a>Adicionar uma lista de negações

Este é o cenário mais comum, em que sua organização deseja trabalhar com praticamente qualquer organização, mas quer impedir que os utilizadores de domínios específicos para ser convidado como utilizadores B2B.

Para adicionar uma lista de negações:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
3. Sob **utilizadores externos**, selecione **gerir definições de colaboração externa**.
4. Sob **restrições de colaboração**, selecione **negar convites para os domínios especificados**.
5. Sob **domínios de destino**, introduza o nome de um dos domínios que pretende bloquear. Para vários domínios, insira cada domínio numa nova linha. Por exemplo:

   ![Mostra a opção negar com domínios adicionados](./media/allow-deny-list/DenyListSettings.png)
 
6. Quando tiver terminado, clique em **guardar**.

Depois de definir a política, se tentar convidar um utilizador a partir de um domínio bloqueado, receberá uma mensagem a indicar que o domínio do utilizador está bloqueado pela sua política de convite.
 
### <a name="add-an-allow-list"></a>Adicionar uma lista de permissões

Esta é uma configuração mais restritiva, onde pode definir domínios específicos na lista de permissões e restringir os convites para outras organizações ou domínios que não são mencionados. 

Se pretender utilizar uma lista de permissões, certifique-se de que gasta tempo para avaliar completamente o que o seu negócio precisa são. Se fizer essa diretiva muito restritivas, os utilizadores podem optar por enviar documentos por e-mail ou Descubra outras maneiras de não-IT aprovadas de colaboração.


Para adicionar uma lista de permissões:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
3. Sob **utilizadores externos**, selecione **gerir definições de colaboração externa**.
4. Sob **restrições de colaboração**, selecione **permitir convites apenas aos domínios especificados (mais restritivo)**.
5. Sob **domínios de destino**, introduza o nome de um dos domínios que pretende permitir. Para vários domínios, insira cada domínio numa nova linha. Por exemplo:

   ![Mostra a opção de permitir com domínios adicionados](./media/allow-deny-list/AllowListSettings.png)
 
6. Quando tiver terminado, clique em **guardar**.

Depois de definir a política, se tentar convidar um utilizador a partir de um domínio que não está na lista de permissões, receberá uma mensagem a indicar que o domínio do utilizador está bloqueado pela sua política de convite.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Opção de permitir para negar lista e vice-versa 

Se mudar de uma política para o outro, isso elimina a configuração da política existente. Certifique-se criar cópias de segurança detalhes da sua configuração antes de executar o comutador. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Defina a permitir ou negar a política de lista com o PowerShell

### <a name="prerequisite"></a>Pré-requisito

Para definir a permitir ou negar lista com o PowerShell, tem de instalar a versão de pré-visualização do módulo Azure Active Directory para Windows PowerShell. Especificamente, instale o versão do módulo 2.0.0.98 do AzureADPreview ou posterior.

Para verificar a versão do módulo (e ver se está instalado):
 
1. Abra o Windows PowerShell como um utilizador elevados (executar como administrador). 
2. Execute o seguinte comando para ver se tem as versões do módulo Azure Active Directory para Windows PowerShell instalado no seu computador:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Se o módulo não está instalado ou não tiver uma versão necessária, efetue um dos seguintes procedimentos:

- Se não são devolvidos resultados, execute o seguinte comando para instalar a versão mais recente do módulo do AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo AzureAD é apresentado nos resultados, execute os seguintes comandos para instalar o módulo do AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se apenas o módulo do AzureADPreview é apresentado nos resultados, mas a versão é inferior a 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Se o AzureAD e AzureADPreview módulos são apresentados nos resultados, mas a versão do módulo do AzureADPreview é inferior a 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Utilizar os cmdlets de AzureADPolicy para configurar a política

Para criar um permitir ou negar lista, utilize o [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet. O exemplo seguinte mostra como definir uma lista de negações bloquear o domínio de "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

O seguinte mostra o mesmo exemplo, mas com o inline de definição de política.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Para definir a permitir ou negar a política de lista, utilize o [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Por exemplo:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Para obter a política, utilize o [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Por exemplo:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Para remover a política, utilize o [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Por exemplo:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do Azure AD B2B, consulte [o que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- Para obter informações sobre o acesso condicional e de colaboração B2B, consulte [acesso condicional para utilizadores de colaboração B2B](conditional-access.md).



