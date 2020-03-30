---
title: Permitir ou bloquear convites para organizações específicas - Azure AD
description: Mostra como um administrador pode usar o portal Azure ou powerShell para definir uma lista de acesso ou negar para permitir ou bloquear utilizadores B2B de determinados domínios.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273429"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Allow or block invitations to B2B users from specific organizations (Permitir ou bloquear convites para utilizadores B2B de organizações específicas)

Pode utilizar uma lista de licenças ou uma lista de negação para permitir ou bloquear convites a utilizadores B2B de organizações específicas. Por exemplo, se quiser bloquear domínios de endereços de e-mail pessoais, pode configurar uma lista de negação que contenha domínios como Gmail.com e Outlook.com. Ou, se o seu negócio tiver uma parceria com outros negócios como Contoso.com, Fabrikam.com e Litware.com, e quiser restringir convites apenas a estas organizações, pode adicionar Contoso.com, Fabrikam.com e Litware.com à sua lista de autorizações.
  
## <a name="important-considerations"></a>Considerações importantes

- Pode criar uma lista de permitir ou uma lista de negação. Não pode configurar os dois tipos de listas. Por padrão, quaisquer domínios que não estejam na lista de licenças estão na lista de negação, e vice-versa. 
- Só se pode criar uma política por organização. Pode atualizar a política para incluir mais domínios, ou pode eliminar a política para criar uma nova. 
- O número de domínios que pode adicionar a uma lista de permitir ou negar lista é limitado apenas pelo tamanho da apólice. O tamanho máximo de toda a política é de 25 KB (25.000 caracteres), que inclui a lista de permitir ou negar lista e quaisquer outros parâmetros configurados para outras funcionalidades.
- Esta lista funciona independentemente do OneDrive para empresas e listas de permitir/bloquear online do OneDrive. Se pretender restringir a partilha individual de ficheiros no SharePoint Online, precisa de configurar uma lista de permitir ou negar uma lista para o OneDrive para Negócios e SharePoint Online. Para mais informações, consulte a partilha de [domínios restritos no SharePoint Online e no OneDrive para negócios](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- A lista não se aplica a utilizadores externos que já tenham resgatado o convite. A lista será executada após a configuração da lista. Se um convite ao utilizador estiver em estado pendente, e definir uma política que bloqueie o seu domínio, a tentativa do utilizador de resgatar o convite falhará.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Desdefinir a política de lista de permitir ou negar no portal

Por predefinição, os convites permitir a envio para qualquer definição de **domínio (mais inclusiva)** estão ativados. Neste caso, pode convidar utilizadores B2B de qualquer organização.

### <a name="add-a-deny-list"></a>Adicione uma lista de negação

Este é o cenário mais típico, onde a sua organização quer trabalhar com quase todas as organizações, mas quer impedir que os utilizadores de domínios específicos sejam convidados como utilizadores B2B.

Para adicionar uma lista de negação:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione**as definições**de**utilizador** >  **do Diretório** > Ativo Azure .
3. Em **utilizadores externos,** selecione **Gerir as definições**de colaboração externa .
4. Sob restrições de **Colaboração,** selecione **convites Denegação para os domínios especificados**.
5. Em **DOMÍNIOS ALVO**, introduza o nome de um dos domínios que pretende bloquear. Para vários domínios, introduza cada domínio numa nova linha. Por exemplo:

   ![Mostra a opção de negar com domínios adicionados](./media/allow-deny-list/DenyListSettings.png)
 
6. Quando terminar, clique em **Guardar**.

Depois de definir a apólice, se tentar convidar um utilizador de um domínio bloqueado, recebe uma mensagem a dizer que o domínio do utilizador está atualmente bloqueado pela sua política de convites.
 
### <a name="add-an-allow-list"></a>Adicione uma lista de permitir

Esta é uma configuração mais restritiva, onde você pode definir domínios específicos na lista de permitir e restringir convites para quaisquer outras organizações ou domínios que não sejam mencionados. 

Se quiser utilizar uma lista de autorizações, certifique-se de que passa tempo para avaliar plenamente quais são as necessidades do seu negócio. Se tornar esta política demasiado restritiva, os seus utilizadores podem optar por enviar documentos por e-mail ou encontrar outras formas de colaboração não-IT.


Para adicionar uma lista de permitir:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione**as definições**de**utilizador** >  **do Diretório** > Ativo Azure .
3. Em **utilizadores externos,** selecione **Gerir as definições**de colaboração externa .
4. Sob restrições de **colaboração,** selecione **Permitir convites apenas para os domínios especificados (mais restritivos)**.
5. Em **DOMÍNIOS ALVO,** introduza o nome de um dos domínios que pretende permitir. Para vários domínios, introduza cada domínio numa nova linha. Por exemplo:

   ![Mostra a opção de permitir com domínios adicionados](./media/allow-deny-list/AllowListSettings.png)
 
6. Quando terminar, clique em **Guardar**.

Depois de definir a apólice, se tentar convidar um utilizador de um domínio que não esteja na lista de autorizações, recebe uma mensagem a dizer que o domínio do utilizador está atualmente bloqueado pela sua política de convites.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Mudar de permitir negar lista e vice-versa 

Se mudar de uma política para outra, isto descarta a configuração da política existente. Certifique-se de que repõe os detalhes da sua configuração antes de executar o interruptor. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Desdefinir a política de lista de permitir ou negar usando o PowerShell

### <a name="prerequisite"></a>Pré-requisito

> [!Note]
> O Módulo AzureADPreview não é um módulo totalmente suportado como está na pré-visualização. 

Para definir a lista de permitir ou negar utilizando o PowerShell, tem de instalar a versão de pré-visualização do Módulo de Diretório Ativo Azure para o Windows PowerShell. Especificamente, instale a versão do módulo AzureADPreview 2.0.0.98 ou posterior.

Para verificar a versão do módulo (e ver se está instalado):
 
1. Abra o Windows PowerShell como um utilizador elevado (Executar como Administrador). 
2. Execute o seguinte comando para ver se tem alguma versão do Módulo de Diretório Ativo Azure para windows PowerShell instalado no seu computador:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Se o módulo não estiver instalado, ou se não tiver uma versão necessária, faça uma das seguintes:

- Se não forem devolvidos resultados, execute o seguinte comando para instalar a versão mais recente do módulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo AzureAD for mostrado nos resultados, execute os seguintes comandos para instalar o módulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se apenas o módulo AzureADPreview for mostrado nos resultados, mas a versão é inferior a 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Se os módulos AzureAD e AzureADPreview forem mostrados nos resultados, mas a versão do módulo AzureADPreview é inferior a 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Utilize os cmdlets AzureADPolicy para configurar a política

Para criar uma lista de permitir ou negar, utilize o cmdlet [New-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) O exemplo que se segue mostra como definir uma lista de negação que bloqueia o domínio "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

O seguinte mostra o mesmo exemplo, mas com a definição de política inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Para definir a política de lista de permitir ou negar, utilize o cmdlet [Set-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) Por exemplo:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Para obter a apólice, use o cmdlet [Get-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) Por exemplo:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Para remover a política, utilize o cmdlet [Remove-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) Por exemplo:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do Azure AD B2B, veja o que é a [colaboração Azure AD B2B?](what-is-b2b.md)
- Para obter informações sobre acesso condicional e colaboração B2B, consulte Acesso Condicional para utilizadores de [colaboração B2B.](conditional-access.md)



