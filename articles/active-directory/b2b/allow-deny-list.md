---
title: Permitir ou bloquear convites para organizações específicas – Azure Active Directory | Microsoft Docs
description: Mostra como um administrador pode usar o portal do Azure ou o PowerShell para definir uma lista de acesso ou de negação para permitir ou bloquear usuários B2B de determinados domínios.
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
ms.openlocfilehash: 62cbe68bcf191c7ee6fc906bc8ba8ea66e3efb31
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233888"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para utilizadores B2B de organizações específicos

Você pode usar uma lista de permissões ou uma lista de negações para permitir ou bloquear convites para usuários B2B de organizações específicas. Por exemplo, se você quiser bloquear domínios de endereço de email pessoal, poderá configurar uma lista de negações que contém domínios como Gmail.com e Outlook.com. Ou, se sua empresa tiver uma parceria com outras empresas, como Contoso.com, Fabrikam.com e Litware.com, e você quiser restringir convites apenas para essas organizações, poderá adicionar Contoso.com, Fabrikam.com e Litware.com à sua lista de permissões.
  
## <a name="important-considerations"></a>Considerações importantes

- Você pode criar uma lista de permissões ou uma lista de negações. Você não pode configurar ambos os tipos de listas. Por padrão, quaisquer domínios que não estejam na lista de permissões estão na lista de negações e vice-versa. 
- Você pode criar apenas uma política por organização. Você pode atualizar a política para incluir mais domínios ou pode excluir a política para criar uma nova. 
- O número de domínios que você pode adicionar a uma lista de permissões ou lista de negações é limitado apenas pelo tamanho da política. O tamanho máximo de toda a política é 25 KB (25.000 caracteres), que inclui a lista de permissões ou a lista de negações e quaisquer outros parâmetros configurados para outros recursos.
- Essa lista funciona independentemente das listas de permissão/bloqueio do OneDrive for Business e do SharePoint Online. Se você quiser restringir o compartilhamento de arquivos individuais no SharePoint Online, precisará configurar uma lista de permissões ou negações para o OneDrive for Business e o SharePoint Online. Para obter mais informações, consulte [compartilhamento de domínios restritos no SharePoint Online e onedrive for Business](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- A lista não se aplica a usuários externos que já resgataram o convite. A lista será imposta depois que a lista for configurada. Se um convite de usuário estiver em um estado pendente e você definir uma política que bloqueia seu domínio, a tentativa do usuário de resgatar o convite falhará.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Definir a política de lista de permissão ou negação no portal

Por padrão, a configuração **permitir que os convites sejam enviados a qualquer domínio (mais inclusivo)** está habilitada. Nesse caso, você pode convidar usuários B2B de qualquer organização.

### <a name="add-a-deny-list"></a>Adicionar uma lista de negações

Esse é o cenário mais típico, em que sua organização deseja trabalhar com quase qualquer organização, mas quer impedir que os usuários de domínios específicos sejam convidados como usuários B2B.

Para adicionar uma lista de negações:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **configurações de usuário** **dos usuários** > .
3. Em **usuários externos**, selecione **gerenciar configurações de colaboração externas**.
4. Em **restrições de colaboração**, selecione **negar convites para os domínios especificados**.
5. Em **domínios de destino**, insira o nome de um dos domínios que você deseja bloquear. Para vários domínios, insira cada domínio em uma nova linha. Por exemplo:

   ![Mostra a opção Deny com domínios adicionados](./media/allow-deny-list/DenyListSettings.png)
 
6. Quando tiver terminado, clique em **guardar**.

Depois de definir a política, se você tentar convidar um usuário de um domínio bloqueado, receberá uma mensagem informando que o domínio do usuário está bloqueado no momento pela sua política de convite.
 
### <a name="add-an-allow-list"></a>Adicionar uma lista de permissões

Essa é uma configuração mais restritiva, na qual você pode definir domínios específicos na lista de permissões e restringir convites a quaisquer outras organizações ou domínios que não são mencionados. 

Se você quiser usar uma lista de permissões, certifique-se de que você gaste muito tempo para avaliar completamente quais são suas necessidades de negócios. Se você tornar essa política muito restritiva, os usuários poderão optar por enviar documentos por email ou encontrar outras maneiras não comsanções de colaborar.


Para adicionar uma lista de permissões:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **configurações de usuário** **dos usuários** > .
3. Em **usuários externos**, selecione **gerenciar configurações de colaboração externas**.
4. Em **restrições de colaboração**, selecione **permitir convites somente para os domínios especificados (mais restritivo)** .
5. Em **domínios de destino**, insira o nome de um dos domínios que você deseja permitir. Para vários domínios, insira cada domínio em uma nova linha. Por exemplo:

   ![Mostra a opção Allow com domínios adicionados](./media/allow-deny-list/AllowListSettings.png)
 
6. Quando tiver terminado, clique em **guardar**.

Depois de definir a política, se você tentar convidar um usuário de um domínio que não está na lista de permissões, você receberá uma mensagem informando que o domínio do usuário está bloqueado no momento pela sua política de convite.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Alternar de permitir lista de negações e vice-versa 

Se você alternar de uma política para a outra, isso descartará a configuração de política existente. Certifique-se de fazer backup dos detalhes da sua configuração antes de executar a opção. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Definir a política de lista de permissões ou negações usando o PowerShell

### <a name="prerequisite"></a>Pré-requisito

Para definir a lista de permissão ou negação usando o PowerShell, você deve instalar a versão de visualização do módulo Azure Active Directory para o Windows PowerShell. Especificamente, instale o módulo AzureADPreview versão 2.0.0.98 ou posterior.

Para verificar a versão do módulo (e ver se ele está instalado):
 
1. Abra o Windows PowerShell como um usuário elevado (executar como administrador). 
2. Execute o seguinte comando para ver se você tem alguma versão do módulo Azure Active Directory para o Windows PowerShell instalado em seu computador:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Se o módulo não estiver instalado ou se você não tiver uma versão necessária, siga um destes procedimentos:

- Se nenhum resultado for retornado, execute o seguinte comando para instalar a versão mais recente do módulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo AzureAD for mostrado nos resultados, execute os seguintes comandos para instalar o módulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se apenas o módulo AzureADPreview for mostrado nos resultados, mas a versão for menor que 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Se os módulos AzureAD e AzureADPreview forem mostrados nos resultados, mas a versão do módulo AzureADPreview for menor que 2.0.0.98, execute os seguintes comandos para atualizá-lo: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Usar os cmdlets AzureADPolicy para configurar a política

Para criar uma lista de permissão ou negação, use o cmdlet [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) . O exemplo a seguir mostra como definir uma lista de negações que bloqueia o domínio "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

A seguir, é mostrado o mesmo exemplo, mas com a definição de política embutida.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Para definir a política de lista de permissão ou negação, use o cmdlet [set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) . Por exemplo:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Para obter a política, use o cmdlet [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) . Por exemplo:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Para remover a política, use o cmdlet [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) . Por exemplo:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral do Azure AD B2B, consulte [o que é a colaboração B2B do Azure ad?](what-is-b2b.md)
- Para obter informações sobre acesso condicional e colaboração B2B, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).



