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
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519611"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configure como os utilizadores finais consentem com as aplicações

As aplicações podem integrar-se na plataforma Microsoft Identity para permitir que os utilizadores assinem o seu trabalho ou conta escolar no Azure Ative Directory (Azure AD), e aceder aos dados da sua organização para fornecer experiências ricas baseadas em dados. Diferentes permissões permitem a aplicação diferente do nível de acesso aos dados dos seus utilizadores e da sua organização.

Por padrão, os utilizadores podem consentir em aplicações de acesso aos dados da sua organização, embora apenas para algumas permissões. Por exemplo, por padrão, um utilizador pode consentir em permitir que uma aplicação aceda à sua própria caixa de correio ou às conversas das Equipas para uma equipa que o utilizador possui, mas não pode consentir em permitir que uma aplicação aceda a ler e escrever a todos os sites do SharePoint na sua organização. Embora permitir que os utilizadores consintam por si mesmos permite que os utilizadores adquiram facilmente aplicações úteis que se integrem com o Microsoft 365, Azure e outros serviços, pode representar um risco se não for usado e monitorizado cuidadosamente.

A Microsoft recomenda que desaperte futuras operações de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. Se o consentimento do utilizador for desativado, as subvenções de consentimento anteriores continuarão a ser honradas, mas todas as futuras operações de consentimento devem ser realizadas por um administrador. O consentimento da administração em todo o inquilino pode ser solicitado pelos utilizadores através de um fluxo de trabalho integrado de [consentimento](configure-admin-consent-workflow.md) administrativo ou através dos seus próprios processos de suporte. Consulte [cinco passos para garantir a sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md) para mais detalhes.

## <a name="configure-user-consent-to-applications"></a>Configure o consentimento do utilizador para as aplicações
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Desativar ou ativar o consentimento do utilizador do portal Azure

Pode utilizar o portal Azure para desativar ou ativar a capacidade dos utilizadores de consentirem com aplicações de acesso aos dados da sua organização:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Selecione **Diretório Ativo Azure,** depois **aplicações da Enterprise,** em seguida, **definições de utilizador**.
3. Ativar ou desativar o consentimento do utilizador com o controlo rotulado **Os Utilizadores podem consentir em aplicações que acedam aos dados**da empresa em seu nome .
4. (Opcional) Configure o consentimento do [administrador solicita o fluxo de trabalho](configure-admin-consent-workflow.md) para garantir que os utilizadores que não estão autorizados a consentir com uma aplicação podem solicitar aprovação.

> [!TIP]
> Para permitir que os utilizadores solicitem a revisão de uma aplicação que o utilizador não está autorizado a consentir (por exemplo, porque o consentimento do utilizador foi desativado, ou porque a aplicação está a solicitar permissões que o utilizador não está autorizado a conceder), considere configurar o fluxo de trabalho de [consentimento do administrador.](configure-admin-consent-workflow.md)

### <a name="disable-or-enable-user-consent-using-powershell"></a>Desative ou ative o consentimento do utilizador usando o PowerShell

Pode utilizar o módulo Azure AD PowerShell v1[(MSOnline),](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)para permitir ou desativar a capacidade dos utilizadores de consentirem com as aplicações de acesso aos dados da sua organização.

1. Inscreva-se na sua organização executando este cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Verifique se o consentimento do utilizador está ativado executando este cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Ativar ou desativar o consentimento do utilizador. Por exemplo, para desativar o consentimento do utilizador, execute este cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configure o consentimento do proprietário do grupo para apps que acedem aos dados do grupo

> [!IMPORTANT]
> As seguintes informações são para uma próxima funcionalidade que permitirá aos proprietários de grupos conceder acesso aos dados dos seus grupos. Quando esta capacidade for libertada, esta será ativada por padrão. Apesar de esta funcionalidade ainda não ter sido amplamente lançada, pode utilizar estas instruções para desativar a capacidade antes da sua libertação.

Os proprietários de grupos podem autorizar aplicações (por exemplo, aplicações publicadas por fornecedores de terceiros) para aceder aos dados da sua organização associados a um grupo. Por exemplo, um dono de equipa (que é o proprietário do Grupo 365 do Office para a equipa) pode permitir que uma aplicação leia todas as mensagens das Equipas na equipa ou enumere o perfil básico dos membros de um grupo.

> [!NOTE]
> Independentemente desta configuração, o proprietário do grupo é sempre autorizado a adicionar outros utilizadores ou aplicações diretamente como proprietários de grupos.

### <a name="configure-group-owner-consent-using-powershell"></a>Configure o consentimento do proprietário do grupo usando powerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell[(AzureADPreview),](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para permitir ou desativar a capacidade dos proprietários do grupo de consentirem com aplicações de acesso aos dados da sua organização para os grupos que possuem.

1. Certifique-se de que está a utilizar o módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (este passo é importante se tiver instalado tanto o módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) como o módulo [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Ligue-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Recupere o valor atual para as definições de diretório de *Definições* de Política de Consentimento no seu inquilino. Isto requer verificar se as definições de diretório para esta funcionalidade foram criadas e, se não, utilizando os valores do modelo de definições de diretório correspondente.

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

4. Compreenda os valores de definição. Existem dois valores de definição que definem quais os utilizadores que seriam capazes de permitir que uma aplicação aceda aos dados do seu grupo:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleano |  Bandeira indicando se os proprietários de grupos são autorizados a conceder permissões específicas do grupo. |
    | _Constrazgroupspecificconsenttomembersofgroupid_ | GUID | Se o _EnableGroupSpecificConsent_ for definido como "True" e este valor definido para o ID de objeto de um grupo, os membros do grupo identificado serão autorizados a conceder permissões específicas do grupo aos grupos que possuem. |

5. Atualizar valores de definições para a configuração desejada:

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

6. Guardar as definições.

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

O consentimento de step-up baseado no risco ajuda a reduzir a exposição do utilizador a aplicações maliciosas que fazem [pedidos de consentimento ilícitos.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Se a Microsoft detetar um pedido de consentimento arriscado do utilizador final, o pedido exigirá um "step-up" para o consentimento da administração. Esta capacidade é ativada por padrão, mas só resultará numa mudança de comportamento quando o consentimento do utilizador final estiver ativado.

Quando um pedido de consentimento arriscado for detetado, o pedido de consentimento apresentará uma mensagem indicando que a aprovação do administrador é necessária. Se o fluxo de trabalho do pedido de [consentimento do administrador](configure-admin-consent-workflow.md) estiver ativado, o utilizador pode enviar o pedido a um administrador para posterior revisão diretamente a partir do pedido de consentimento. Se não estiver ativada, será exibida a seguinte mensagem:

* **AADSTS90094:** &lt;o clienteAppDisplayName&gt; precisa de autorização para aceder a recursos na sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Neste caso, será também registado um evento de auditoria com uma categoria de "Gestão de Aplicações", Tipo de Atividade de "Consentimento para aplicação" e Motivo de Estado da "aplicação arriscada detetada".

> [!IMPORTANT]
> Os administradores devem [avaliar cuidadosamente todos os pedidos](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) de consentimento antes de aprovar, especialmente quando a Microsoft detetou o risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desative ou reative o consentimento baseado no risco usando powerShell

Pode utilizar o módulo de pré-visualização Azure AD PowerShell[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)para desativar o consentimento de administração necessário nos casos em que a Microsoft detete riscos ou o reactivase se este fosse previamente desativado.

Isto pode ser feito utilizando os mesmos passos acima indicados para configurar o consentimento do [proprietário do grupo usando powerShell,](#configure-group-owner-consent-using-powershell)mas substituindo um valor de configurações diferente. Há três diferenças nos passos: 

1. Compreender os valores de definição para o consentimento de step-up baseado no risco:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForriskyApps_   | Booleano |  A bandeira indicando se o consentimento do utilizador será bloqueado quando um pedido de risco for detetado. |

2. Substitua o seguinte valor na etapa 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Substitua um dos seguintes passos no passo 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Passos seguintes

[Configure o fluxo de trabalho de consentimento da administração](configure-admin-consent-workflow.md)

[Saiba como gerir o consentimento para as candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)

[Conceda o consentimento do administrador de todo o inquilino a um pedido](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Anúncio Azure em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
