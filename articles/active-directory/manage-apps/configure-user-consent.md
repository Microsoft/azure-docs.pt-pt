---
title: Configure como os utilizadores finais concordam com aplicações usando Azure AD
description: Saiba como e quando os utilizadores podem consentir com aplicações que terão acesso aos dados da sua organização.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763470"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurar a forma como os utilizadores finais concedem consentimento às aplicações

Pode integrar as suas aplicações com a plataforma de identidade da Microsoft para permitir que os utilizadores entrem com o seu trabalho ou conta escolar e acedam aos dados da sua organização para fornecer experiências ricas baseadas em dados.

Antes que uma aplicação possa aceder aos dados da sua organização, um utilizador deve conceder as permissões de aplicação para o fazer. Diferentes permissões permitem diferentes níveis de acesso. Por predefinição, todos os utilizadores estão autorizados a consentir em pedidos de permissões que não requerem o consentimento do administrador. Por exemplo, por padrão, um utilizador pode consentir em permitir que uma aplicação aceda à sua caixa de correio, mas não pode consentir em permitir que uma aplicação tenha acesso livre para ler e escrever a todos os ficheiros da sua organização.

Ao permitir que os utilizadores concedam às apps o acesso aos dados, os utilizadores podem facilmente adquirir aplicações úteis e ser produtivos. No entanto, em algumas situações esta configuração pode representar um risco se não for monitorizada e controlada cuidadosamente.

## <a name="user-consent-settings"></a>Definições de consentimento do utilizador

Para controlar que casos os utilizadores podem consentir com as aplicações, escolha a política de consentimento que se aplicará a todos os utilizadores. Aqui estão as três opções de política de consentimento:

* **Desativar o consentimento do utilizador** - Os utilizadores não podem conceder permissões às aplicações. Os utilizadores podem continuar a iniciar seduções em apps que tinham previamente consentido ou que são consentidas por administradores em seu nome, mas não serão autorizados a consentir com novas permissões ou novas aplicações por conta própria. Apenas os utilizadores que tenham recebido um papel de diretório que inclua a permissão para conceder o consentimento poderão consentir com novas permissões ou novas aplicações.

* **Os utilizadores podem consentir com aplicações de editoras verificadas, mas apenas para permissões que selecione (pré-visualização)** - Todos os utilizadores só podem consentir com aplicações que foram publicadas por um [editor verificado](../develop/publisher-verification-overview.md) e aplicações que estão registadas no seu inquilino. Os utilizadores só podem consentir com as permissões que classificou como "Baixo impacto".

  Certifique-se [de classificar permissões](#configure-permission-classifications-preview) para selecionar quais permissões os utilizadores estão autorizados a consentir.

* **Os utilizadores podem consentir com todas as aplicações** - Esta opção permite que todos os utilizadores consintam em qualquer permissão, que não requer consentimento administrativo, para qualquer aplicação. 

   Para reduzir o risco de aplicações maliciosas que tentam enganar os utilizadores a conceder-lhes acesso aos dados da sua organização, recomendamos que permita o consentimento do utilizador apenas para aplicações que tenham sido publicadas por um [editor verificado.](../develop/publisher-verification-overview.md)

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Configurar as definições de consentimento do utilizador a partir do portal Azure

Para configurar as definições de consentimento do utilizador através do portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consent and permissions**User consent  >  **settings**.
1. De acordo com **o consentimento do Utilizador para aplicações,** selecione qual a definição de consentimento que pretende configurar para todos os utilizadores.
1. **Selecione Guardar** para guardar as suas definições.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Definições de consentimento do utilizador":::

> [!TIP]
> Considere [permitir que o fluxo de trabalho](configure-admin-consent-workflow.md) de consentimento administrativo permita que os utilizadores solicitem a revisão e aprovação de um administrador de uma aplicação que o utilizador não está autorizado a consentir-- por exemplo, quando o consentimento do utilizador foi desativado ou quando um pedido está a solicitar permissões que o utilizador não está autorizado a conceder.

### <a name="configure-user-consent-settings-using-powershell"></a>Configurar as definições de consentimento do utilizador utilizando o PowerShell

Pode utilizar o mais recente módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)para escolher qual a política de consentimento que rege o consentimento do utilizador para aplicações.

* **Desativar o consentimento do utilizador** - Para desativar o consentimento do utilizador, desintebilita as políticas de consentimento que regem o consentimento do utilizador para estarem vazias:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Permitir o consentimento do utilizador para apps de editores verificados, para permissões selecionadas (pré-visualização)** - Para permitir o consentimento limitado do utilizador apenas para aplicações de editores e aplicações verificadas registadas no seu inquilino, e apenas para permissões que classifica como "Baixo impacto", configure a política de consentimento incorporada denominada: `microsoft-user-default-low`

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Não se esqueça de [classificar permissões](#configure-permission-classifications-preview) para selecionar quais as permissões a que os utilizadores estão autorizados a consentir.

* **Permitir o consentimento do utilizador para todas as aplicações** - Para permitir o consentimento do utilizador para todas as aplicações:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Esta opção permite que todos os utilizadores consintam em qualquer permissão que não exija consentimento administrativo, para qualquer aplicação. Recomendamos que permita o consentimento do utilizador apenas para apps de editores verificados.

## <a name="configure-permission-classifications-preview"></a>Configurar classificações de permissão (pré-visualização)

As classificações de permissão permitem identificar o impacto que as diferentes permissões têm de acordo com as políticas da sua organização e avaliações de risco. Por exemplo, pode utilizar classificações de permissão em políticas de consentimento para identificar o conjunto de permissões a que os utilizadores estão autorizados a consentir.

> [!NOTE]
> Atualmente, apenas a classificação de permissão de "baixo impacto" é suportada. Apenas permissões delegadas que não exijam consentimento administrativo podem ser classificadas como "Baixo impacto".

### <a name="classify-permissions-using-the-azure-portal"></a>Classificar permissões usando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise Aplicações**  >  **Consentimento e permissões**  >  **Classificações de permissão**.
1. Escolha **Permissões de adicionar** para classificar outra permissão como "Baixo impacto". 
1. Selecione a API e, em seguida, selecione as permissões delegadas.

Neste exemplo, classificamos o conjunto mínimo de permissão necessária para uma única inscrição:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Classificações de permissão":::

> [!TIP]
> Para a API do Gráfico microsoft, as permissões mínimas necessárias para fazer um único sinal básico são `openid` `profile` , e `User.Read` `offline_access` . Com estas permissões, uma aplicação pode ler os detalhes do perfil do utilizador inscrito e pode manter este acesso mesmo quando o utilizador já não estiver a utilizar a app.

### <a name="classify-permissions-using-powershell"></a>Classificar permissões usando PowerShell

Pode utilizar o mais recente módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para classificar permissões. As classificações de permissão são configuradas no objeto **ServicePrincipal** da API que publica as permissões.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Para ler as classificações de permissão atuais para uma API:

1. Recupere o objeto **ServicePrincipal** para a API. Aqui recuperamos o objeto ServicePrincipal para a API do Gráfico microsoft:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Leia as classificações de autorização delegada para a API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Para classificar uma permissão como "Baixo impacto":

1. Recupere o objeto **ServicePrincipal** para a API. Aqui recuperamos o objeto ServicePrincipal para a API do Gráfico microsoft:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Encontre a permissão delegada que gostaria de classificar:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Desa estada a classificação de permissão utilizando o nome de permissão e identificação:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Para remover uma classificação de permissão delegada:

1. Recupere o objeto **ServicePrincipal** para a API. Aqui recuperamos o objeto ServicePrincipal para a API do Gráfico microsoft:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Encontre a classificação de permissão delegada que deseja remover:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Eliminar a classificação de permissão:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configure o consentimento do proprietário do grupo para apps que acedam aos dados do grupo

Os proprietários do grupo podem autorizar aplicações, como aplicações publicadas por fornecedores de terceiros, a aceder aos dados da sua organização associados a um grupo. Por exemplo, um dono de equipa em Microsoft Teams pode permitir que uma aplicação leia todas as mensagens de Equipas na equipa ou liste o perfil básico dos membros de um grupo.

Pode configurar quais os utilizadores que estão autorizados a consentir em aplicações que acedam aos dados dos seus grupos, ou pode desativar esta funcionalidade.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Configurar o consentimento do proprietário do grupo usando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consent and permissions**User consent  >  **settings**.
3. Sob **o consentimento do proprietário do Grupo para aplicações que acedam a dados** selecione a opção que gostaria de ativar.
4. **Selecione Guardar** para guardar as suas definições.

Neste exemplo, todos os proprietários do grupo estão autorizados a consentir que as aplicações acedam aos dados dos seus grupos:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Definições de consentimento do proprietário do grupo":::

### <a name="configure-group-owner-consent-using-powershell"></a>Configure o consentimento do proprietário do grupo usando o PowerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para permitir ou desativar a capacidade dos proprietários do grupo de consentirem com as aplicações que acedem aos dados da sua organização para os grupos que possuem.

1. Certifique-se de que está a utilizar o módulo [AzureADPreview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) Este passo é importante se tiver instalado o módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) e o módulo [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Ligue-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recupere o valor atual para as definições de **diretório de Definições de Consentimento** no seu inquilino. Isto requer a verificação se as definições de diretório para esta função foram criadas e, se não, utilizando os valores do modelo de definições de diretório correspondente.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Compreenda os valores de definição. Existem dois valores de definição que definem quais os utilizadores que seriam capazes de permitir que uma aplicação acedesse aos dados do seu grupo:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _Ativar oGroupSpecificConsent_   | Booleano | Bandeira indicando se os proprietários de grupos são autorizados a conceder permissões específicas do grupo. |
    | _RestriçãoGroupSpecificConsentToMemembersOfGroupId_ | GUID | Se _o EnableGroupSpecificConsent_ estiver definido como "Verdadeiro" e este valor definido para o ID de objeto de um grupo, os membros do grupo identificado serão autorizados a conceder permissões específicas do grupo aos grupos que possuem. |

1. Atualizar valores de definições para a configuração desejada:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Guarde as suas definições.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configure o consentimento de um passo de intensificação baseado no risco

O consentimento de intensificação baseado no risco ajuda a reduzir a exposição do utilizador a aplicações maliciosas que fazem [pedidos de consentimento ilícitos.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se a Microsoft detetar um pedido de consentimento de utilizador final arriscado, o pedido exigirá um "step-up" para obter o consentimento administrativo. Esta capacidade é ativada por padrão, mas só resultará numa mudança de comportamento quando o consentimento do utilizador final estiver ativado.

Quando um pedido de consentimento de risco for detetado, o pedido de consentimento apresentará uma mensagem indicando que a aprovação de administração é necessária. Se o fluxo de trabalho do [pedido de consentimento administrativo](configure-admin-consent-workflow.md) estiver ativado, o utilizador pode enviar o pedido a um administrador para posterior revisão diretamente a partir do pedido de consentimento. Se não estiver ativada, será exibida a seguinte mensagem:

* **AADSTS90094:** &lt; clienteAppDisplayName &gt; precisa de permissão para aceder a recursos na sua organização que só um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Neste caso, será também registado um evento de auditoria com uma categoria de "Gestação de Aplicações", Tipo de Atividade de "Consentimento à aplicação", e Razão de Estado de "Aplicação arriscada detetada".

> [!IMPORTANT]
> Os administradores devem [avaliar cuidadosamente todos os pedidos de consentimento](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) antes de aprovar um pedido, especialmente quando a Microsoft detetou o risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desativar ou reativar o consentimento de intensificação baseado no risco usando o PowerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para desativar o consentimento de administração necessário para os casos em que a Microsoft deteta riscos ou o reativa se este tiver sido previamente desativado.

Pode fazê-lo utilizando os mesmos passos acima indicados para configurar o [consentimento do proprietário do grupo utilizando o PowerShell,](#configure-group-owner-consent-using-powershell)mas substituindo um valor de configurações diferente. Há três diferenças de etapas: 

1. Compreenda os valores de definição do consentimento de intensificação baseado no risco:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Booleano |  Sinalização indicando se o consentimento do utilizador será bloqueado quando for detetado um pedido de risco. |

1. Substitua o seguinte valor no passo 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Substitua um dos seguintes no passo 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Próximos passos

Para saber mais:

* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento das candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
