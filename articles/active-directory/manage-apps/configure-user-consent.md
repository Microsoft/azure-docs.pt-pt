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
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: c279b7f4ec2d70e7eb6a97abf02cfbf4f2ef87f0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033617"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurar a forma como os utilizadores finais concedem consentimento às aplicações

Pode integrar as suas aplicações com a plataforma de identidade da Microsoft para permitir que os utilizadores entrem com o seu trabalho ou conta escolar e acedam aos dados da sua organização para fornecer experiências ricas baseadas em dados.

Antes que uma aplicação possa aceder aos dados da sua organização, um utilizador deve conceder as permissões de aplicação para o fazer. Diferentes permissões permitem diferentes níveis de acesso. Por predefinição, todos os utilizadores estão autorizados a consentir em pedidos de permissões que não requerem o consentimento do administrador. Por exemplo, por padrão, um utilizador pode consentir em permitir que uma aplicação aceda à sua caixa de correio, mas não pode consentir em permitir que uma aplicação tenha acesso livre para ler e escrever a todos os ficheiros da sua organização.

Ao permitir que os utilizadores concedam às apps o acesso aos dados, os utilizadores podem facilmente adquirir aplicações úteis e ser produtivos. No entanto, em algumas situações esta configuração pode representar um risco se não for monitorizada e controlada cuidadosamente.

> [!IMPORTANT]
> Para reduzir o risco de aplicações maliciosas que tentam enganar os utilizadores a conceder-lhes acesso aos dados da sua organização, recomendamos que permita o consentimento do utilizador apenas para aplicações que tenham sido publicadas por um [editor verificado.](../develop/publisher-verification-overview.md)

## <a name="user-consent-settings"></a>Definições de consentimento do utilizador

As políticas de consentimento da app descrevem as condições que devem ser satisfeitas antes de uma aplicação poder ser consentida. Estas políticas podem incluir condições na aplicação que solicita o acesso, bem como as permissões que a app está a solicitar.

Ao escolher quais as políticas de consentimento da aplicação aplicáveis a todos os utilizadores, pode estabelecer limites para quando os utilizadores finais são autorizados a conceder consentimento às apps, e quando serão obrigados a solicitar a revisão e aprovação do administrador:

* **Desativar o consentimento do utilizador** - Os utilizadores não podem conceder permissões às aplicações. Os utilizadores podem continuar a iniciar seduções em apps que tinham previamente consentido ou que são consentidas por administradores em seu nome, mas não serão autorizados a consentir com novas permissões ou novas aplicações por conta própria. Apenas os utilizadores que tenham recebido um papel de diretório que inclua a permissão para conceder o consentimento poderão consentir com novas aplicações.

* **Os utilizadores podem consentir em apps de editores verificados ou da sua organização, mas apenas para permissões que selecione** - Todos os utilizadores só podem consentir com aplicações que foram publicadas por um [editor verificado](../develop/publisher-verification-overview.md) e aplicações que estão registadas no seu inquilino. Os utilizadores só podem consentir com as permissões que classificou como "baixo impacto". Tem de [classificar permissões](configure-permission-classifications.md) para selecionar quais as permissões a que os utilizadores estão autorizados a consentir.

* **Os utilizadores podem consentir com todas as aplicações** - Esta opção permite que todos os utilizadores consintam em qualquer permissão que não exija consentimento administrativo, para qualquer aplicação.

* **Política de consentimento de aplicativo personalizado** - Para ainda mais opções sobre as condições que regem quando o consentimento do utilizador, pode criar uma política de consentimento de [aplicações personalizadas](manage-app-consent-policies.md#create-a-custom-app-consent-policy)e configurar as que se candidatam ao consentimento do utilizador.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar as definições de consentimento do utilizador através do portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../roles/permissions-reference.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consent and permissions** User consent  >  **settings**.
1. De acordo com **o consentimento do Utilizador para aplicações,** selecione qual a definição de consentimento que pretende configurar para todos os utilizadores.
1. **Selecione Guardar** para guardar as suas definições.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Definições de consentimento do utilizador":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o mais recente módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)para escolher qual a política de consentimento da aplicação que rege o consentimento do utilizador para aplicações.

#### <a name="disable-user-consent"></a>Desativar o consentimento do utilizador

Para desativar o consentimento do utilizador, desinsuse as políticas de consentimento que regem o consentimento do utilizador para estarem vazias:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Permitir o consentimento do utilizador sujeito a uma política de consentimento de aplicações

Para permitir o consentimento do utilizador, escolha qual a política de consentimento da aplicação que deve reger a autorização dos utilizadores para conceder o consentimento às aplicações:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

`{consent-policy-id}`Substitua-o pela identificação da apólice que gostaria de aplicar. Pode escolher uma [política de consentimento de aplicações personalizadas](manage-app-consent-policies.md#create-a-custom-app-consent-policy) que criou ou pode escolher entre as seguintes políticas incorporadas:

| ID | Descrição |
|:---|:------------|
| microsoft-user-default-low | **Permitir o consentimento do utilizador para apps de editores verificados, para permissões selecionadas**<br /> Permitir o consentimento limitado do utilizador apenas para apps de editores e aplicações verificadas registadas no seu inquilino, e apenas para permissões que classifica como "Baixo impacto". (Não se esqueça de [classificar permissões](configure-permission-classifications.md) para selecionar quais as permissões a que os utilizadores estão autorizados a consentir.) |
| microsoft-user-default-legacy | **Permitir o consentimento do utilizador para apps**<br /> Esta opção permite que todos os utilizadores consintam em qualquer permissão que não exija consentimento administrativo, para qualquer aplicação |
  
Por exemplo, para permitir o consentimento do utilizador sujeito à política `microsoft-user-default-low` incorporada:

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Ativar o fluxo de trabalho](configure-admin-consent-workflow.md) de consentimento administrativo para permitir que os utilizadores solicitem a revisão e aprovação de um administrador de uma aplicação que o utilizador não está autorizado a consentir — por exemplo, quando o consentimento do utilizador foi desativado ou quando um pedido está a solicitar permissões que o utilizador não está autorizado a conceder.

## <a name="risk-based-step-up-consent"></a>Consentimento de intensificação baseado no risco

O consentimento de intensificação baseado no risco ajuda a reduzir a exposição do utilizador a aplicações maliciosas que fazem [pedidos de consentimento ilícitos.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se a Microsoft detetar um pedido de consentimento de utilizador final arriscado, o pedido exigirá um "step-up" para obter o consentimento administrativo. Esta capacidade é ativada por padrão, mas só resultará numa mudança de comportamento quando o consentimento do utilizador final estiver ativado.

Quando um pedido de consentimento de risco for detetado, o pedido de consentimento apresentará uma mensagem indicando que a aprovação de administração é necessária. Se o fluxo de trabalho do [pedido de consentimento administrativo](configure-admin-consent-workflow.md) estiver ativado, o utilizador pode enviar o pedido a um administrador para posterior revisão diretamente a partir do pedido de consentimento. Se não estiver ativada, será exibida a seguinte mensagem:

* **AADSTS90094:** &lt; clienteAppDisplayName &gt; precisa de permissão para aceder a recursos na sua organização que só um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Neste caso, será também registado um evento de auditoria com uma categoria de "Gestação de Aplicações", Tipo de Atividade de "Consentimento à aplicação", e Razão de Estado de "Aplicação arriscada detetada".

> [!IMPORTANT]
> Os administradores devem [avaliar cuidadosamente todos os pedidos de consentimento](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) antes de aprovar um pedido, especialmente quando a Microsoft detetou o risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desativar ou reativar o consentimento de intensificação baseado no risco usando o PowerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)para desativar o consentimento de administração necessário para os casos em que a Microsoft deteta riscos ou o reativa se este tiver sido previamente desativado.

1. Certifique-se de que está a utilizar o módulo [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Este passo é importante se tiver instalado o módulo [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) e o módulo [AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Compreender o valor das definições:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Booleano |  Sinalização indicando se o consentimento do utilizador será bloqueado quando for detetado um pedido de risco. |

1. Valor de definições de atualização para a configuração desejada:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Passos seguintes

Para saber mais:

* [Configurar as definições de consentimento do utilizador](configure-user-consent.md)
* [Gerir as políticas de consentimento da aplicação](manage-app-consent-policies.md)
* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento das candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)