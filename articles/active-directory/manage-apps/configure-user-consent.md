---
title: Configure como os utilizadores finais consentem com as aplicações usando a AD Azure
description: Saiba como gerir como e quando os utilizadores podem consentir com aplicações que terão acesso aos dados da sua organização.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 2064ac929063fcdcf15c1e7495769c7d84aeef33
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698080"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configure como os utilizadores finais consentem com as aplicações

Pode integrar as suas aplicações com a plataforma de identidade da Microsoft para permitir que os utilizadores acedam ao seu trabalho ou conta escolar e acedam aos dados da sua organização para fornecer experiências ricas baseadas em dados.

Antes de uma aplicação poder aceder aos dados da sua organização, um utilizador deve conceder as permissões de pedido para o fazer. Diferentes permissões permitem diferentes níveis de acesso. Por padrão, todos os utilizadores estão autorizados a consentir com pedidos de permissões que não requerem o consentimento do administrador. Por exemplo, por padrão, um utilizador pode consentir em permitir que uma aplicação aceda à sua caixa de correio, mas não pode consentir em permitir que uma aplicação tenha acesso sem restrições para ler e escrever a todos os ficheiros da sua organização.

Ao permitir que os utilizadores concedam às aplicações o acesso aos dados, os utilizadores podem facilmente adquirir aplicações úteis e ser produtivos. No entanto, em algumas situações esta configuração pode representar um risco se não for monitorizada e controlada cuidadosamente.

## <a name="user-consent-settings"></a>Definições de consentimento do utilizador

Para controlar quais os casos que os utilizadores podem consentir com as aplicações, escolha a política de consentimento que se aplicará a todos os utilizadores. Aqui estão as três opções de política de consentimento:

* **Desativar** o consentimento do utilizador - Os utilizadores não podem conceder permissões a aplicações. Os utilizadores podem continuar a iniciar sessão em aplicações que previamente consentiram ou que sejam consentidos pelos administradores em seu nome, mas não serão autorizados a consentir novas permissões ou a novas aplicações por conta própria. Apenas os utilizadores que tenham recebido uma função de diretório que inclua a permissão para conceder o consentimento poderão consentir novas permissões ou novas aplicações.

* **Os utilizadores podem consentir com aplicações de editores verificados, mas apenas para permissões que selecione (pré-visualização)** - Todos os utilizadores só podem consentir com aplicações que tenham sido publicadas por um editor e aplicações [verificadas](../develop/publisher-verification-overview.md) que estejam registadas no seu inquilino. Os utilizadores só podem consentir com as permissões que classificou como "baixo impacto".

  Certifique-se de [classificar as permissões](#configure-permission-classifications-preview) para selecionar quais as permissões que os utilizadores podem consentir.

* **Os utilizadores podem consentir com todas as aplicações** - Esta opção permite que todos os utilizadores consintam em qualquer permissão, o que não requer consentimento administrativo, para qualquer aplicação. 

   Para reduzir o risco de aplicações maliciosas que tentam enganar os utilizadores a conceder-lhes acesso aos dados da sua organização, recomendamos que permita o consentimento do utilizador apenas para aplicações que tenham sido publicadas por um [editor verificado.](../develop/publisher-verification-overview.md)

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Configure as definições de consentimento do utilizador do portal Azure

Para configurar as definições de consentimento do utilizador através do portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consentimento e permissão**As  >  **definições**de consentimento do utilizador .
1. Sob o **consentimento do Utilizador para aplicações,** selecione qual a definição de consentimento que deseja configurar para todos os utilizadores.
1. Selecione **Guardar** para guardar as suas definições.

![Definições de consentimento do utilizador](./media/configure-user-consent/configure-consent-setting-for-all-users.png)

> [!TIP]
> Considere [permitir que o fluxo](configure-admin-consent-workflow.md) de trabalho de consentimento do administrador permita que os utilizadores solicitem a revisão e aprovação de uma aplicação que o utilizador não está autorizado a consentir- por exemplo, quando o consentimento do utilizador foi desativado ou quando uma aplicação está a solicitar permissões que o utilizador não está autorizado a conceder.

### <a name="configure-user-consent-settings-using-powershell"></a>Configure as definições de consentimento do utilizador utilizando o PowerShell

Pode utilizar o mais recente módulo de pré-visualização da AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)para escolher qual a política de consentimento que rege o consentimento do utilizador para aplicações.

* **Desativar** o consentimento do utilizador - Para desativar o consentimento do utilizador, detete as políticas de consentimento que regem o consentimento do utilizador para estar vazia:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Permitir o consentimento dos utilizadores para aplicações de editores verificados, para permissões selecionadas (pré-visualização)** - Para permitir o consentimento limitado dos utilizadores apenas para aplicações de editores e aplicações verificadas registadas no seu inquilino, e apenas para permissões que classifica como "Baixo Impacto", configurar a política de consentimento incorporada `microsoft-user-default-low` chamada:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Não se esqueça de [classificar permissões](#configure-permission-classifications-preview) para selecionar quais as permissões que os utilizadores podem consentir.

* **Permitir o consentimento do utilizador para todas as aplicações** - Para permitir o consentimento do utilizador para todas as aplicações:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Esta opção permite a todos os utilizadores consentir em qualquer permissão que não exija consentimento administrativo, para qualquer aplicação. Recomendamos que permita o consentimento do utilizador apenas para aplicações de editores verificados.

## <a name="configure-permission-classifications-preview"></a>Configurar classificações de permissões (pré-visualização)

As classificações de permissões permitem identificar o impacto que diferentes permissões têm de acordo com as políticas da sua organização e avaliações de risco. Por exemplo, pode utilizar classificações de permissões em políticas de consentimento para identificar o conjunto de permissões que os utilizadores podem consentir.

> [!NOTE]
> Atualmente, apenas a classificação de permissão de "baixo impacto" é suportada. Só as permissões delegadas que não requerem consentimento administrativo podem ser classificadas como "baixo impacto".

### <a name="classify-permissions-using-the-azure-portal"></a>Classifique permissões usando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise aplica consentimento**e  >  **permissões**Classificações de  >  **permissão**.
1. Escolha **Adicionar permissões** para classificar outra permissão como "baixo impacto". 
1. Selecione a API e, em seguida, selecione as permissões delegadas.

Neste exemplo, classificamos o conjunto mínimo de permissão necessário para um único sinal:

![Classificações de permissão](./media/configure-user-consent/configure-permission-classifications.png)

> [!TIP]
> Para a API do Microsoft Graph, as permissões mínimas necessárias para fazer um único sinal básico são `openid` `profile` , e `User.Read` `offline_access` . Com estas permissões, uma aplicação pode ler os detalhes do perfil do utilizador inscrito e pode manter este acesso mesmo quando o utilizador já não está a utilizar a aplicação.

### <a name="classify-permissions-using-powershell"></a>Classificar permissões usando powerShell

Pode utilizar o mais recente módulo de pré-visualização Da AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para classificar permissões. As classificações de permissões são configuradas no objeto principal de **serviço** da API que publica as permissões.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Para ler as classificações de permissão atuais para uma API:

1. Recupere o objeto principal de **serviço** para a API. Aqui recuperamos o objeto ServicePrincipal para a Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Leia as classificações de permissão delegadas para a API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Para classificar uma permissão como "baixo impacto":

1. Recupere o objeto principal de **serviço** para a API. Aqui recuperamos o objeto ServicePrincipal para a Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Encontre a permissão delegada que gostaria de classificar:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Detete a classificação da permissão usando o nome de permissão e id:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Para remover uma classificação de permissão delegada:

1. Recupere o objeto principal de **serviço** para a API. Aqui recuperamos o objeto ServicePrincipal para a Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Encontre a classificação de permissão delegada que pretende remover:

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

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configure o consentimento do proprietário do grupo para apps que acedem aos dados do grupo

Os proprietários de grupos podem autorizar aplicações, tais como aplicações publicadas por fornecedores de terceiros, para aceder aos dados da sua organização associados a um grupo. Por exemplo, um dono de uma equipa em Microsoft Teams pode permitir que uma aplicação leia todas as mensagens de Equipas na equipa ou lista o perfil básico dos membros de um grupo.

Pode configurar quais os utilizadores autorizados a consentir em aplicações que acedam aos dados dos seus grupos, ou pode desativar esta funcionalidade.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Configure o consentimento do proprietário do grupo usando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consentimento e permissão**As  >  **definições**de consentimento do utilizador .
3. Segundo o consentimento do proprietário do Grupo para aplicações que **acedem a dados,** selecione a opção que deseja ativar.
4. Selecione **Guardar** para guardar as suas definições.

Neste exemplo, todos os proprietários de grupos estão autorizados a consentir com aplicações que acedam aos dados dos seus grupos:

![Classificações de permissão](./media/configure-user-consent/configure-group-owner-consent.png)

### <a name="configure-group-owner-consent-using-powershell"></a>Configure o consentimento do proprietário do grupo usando powerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para permitir ou desativar a capacidade dos proprietários do grupo de consentirem com aplicações de acesso aos dados da sua organização para os grupos que possuem.

1. Certifique-se de que está a utilizar o módulo [AzureADPreview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) Este passo é importante se tiver instalado tanto o módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) como o módulo [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Ligue-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recupere o valor atual para as definições de diretório de **Definições** de Política de Consentimento no seu inquilino. Isto requer verificar se as definições de diretório para esta funcionalidade foram criadas e, se não, utilizando os valores do modelo de definições de diretório correspondente.

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

1. Compreenda os valores de definição. Existem dois valores de definição que definem quais os utilizadores que seriam capazes de permitir que uma aplicação aceda aos dados do seu grupo:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleano | Bandeira indicando se os proprietários de grupos são autorizados a conceder permissões específicas do grupo. |
    | _Constrazgroupspecificconsenttomembersofgroupid_ | GUID | Se o _EnableGroupSpecificConsent_ for definido como "True" e este valor definido para o ID de objeto de um grupo, os membros do grupo identificado serão autorizados a conceder permissões específicas do grupo aos grupos que possuem. |

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

## <a name="configure-risk-based-step-up-consent"></a>Configure o consentimento de intensificação baseado no risco

O consentimento de step-up baseado no risco ajuda a reduzir a exposição do utilizador a aplicações maliciosas que fazem pedidos de [consentimento ilícitos.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se a Microsoft detetar um pedido de consentimento arriscado do utilizador final, o pedido exigirá um "step-up" para o consentimento da administração. Esta capacidade é ativada por padrão, mas só resultará numa mudança de comportamento quando o consentimento do utilizador final estiver ativado.

Quando um pedido de consentimento arriscado for detetado, o pedido de consentimento apresentará uma mensagem indicando que a aprovação do administrador é necessária. Se o fluxo de trabalho do pedido de [consentimento do administrador](configure-admin-consent-workflow.md) estiver ativado, o utilizador pode enviar o pedido a um administrador para posterior revisão diretamente a partir do pedido de consentimento. Se não estiver ativada, será exibida a seguinte mensagem:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; precisa de permissão para aceder a recursos na sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Neste caso, será também registado um evento de auditoria com uma categoria de "Gestão de Aplicações", Tipo de Atividade de "Consentimento para a aplicação", e Razão de Estado da "aplicação arriscada detetada".

> [!IMPORTANT]
> Os administradores devem [avaliar cuidadosamente todos os pedidos](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) de consentimento antes de aprovar um pedido, especialmente quando a Microsoft detetou o risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desative ou reative o consentimento baseado no risco usando powerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para desativar o consentimento de administração necessário nos casos em que a Microsoft detete riscos ou o reativa se tiver sido previamente desativado.

Pode fazê-lo utilizando os mesmos passos acima indicados para configurar o consentimento do proprietário do grupo utilizando o [PowerShell,](#configure-group-owner-consent-using-powershell)mas substituindo um valor de definições diferente. Há três diferenças nos passos: 

1. Compreender os valores de definição para o consentimento de step-up baseado no risco:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForriskyApps_   | Booleano |  A bandeira indicando se o consentimento do utilizador será bloqueado quando um pedido de risco for detetado. |

1. Substitua o seguinte valor na etapa 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Substitua um dos seguintes passos no passo 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais:

* [Configure o fluxo de trabalho de consentimento da administração](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento para as candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Anúncio Azure em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
