---
title: Configurar como os usuários finais consentim aplicativos usando o Azure AD
description: Saiba como gerenciar como e quando os usuários podem consentir com os aplicativos que terão acesso aos dados da sua organização.
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
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443395"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurar como os usuários finais concordam com os aplicativos

Os aplicativos podem ser integrados com a plataforma de identidade da Microsoft para permitir que os usuários entrem usando sua conta corporativa ou de estudante no Azure Active Directory (Azure AD) e acessem os dados da sua organização para fornecer experiências avançadas controladas por dados. Permissões diferentes permitem que o aplicativo tenha um nível diferente de acesso aos dados dos seus usuários e da sua organização.

Por padrão, os usuários podem consentir os aplicativos que acessam os dados da sua organização, embora apenas algumas permissões. Por exemplo, por padrão, um usuário pode consentir para permitir que um aplicativo acesse suas próprias caixas de correio ou as equipes de conversas de uma equipe que o usuário possui, mas não pode consentir para permitir que um aplicativo acesse a leitura e gravação em todos os sites do SharePoint em sua organização. Embora permitir que os usuários consentissem por si só permita que os usuários adquiram facilmente aplicativos úteis que se integram ao Microsoft 365, ao Azure e a outros serviços, ele pode representar um risco se não for usado e monitorado com cuidado.

A Microsoft recomenda desabilitar operações futuras de consentimento do usuário para ajudar a reduzir a área de superfície e atenuar esse risco. Se o consentimento do usuário for desabilitado, as concessões de consentimento anteriores ainda serão respeitadas, mas todas as operações de consentimento futuras deverão ser executadas por um administrador. O consentimento do administrador em todo o locatário pode ser solicitado pelos usuários por meio de um [fluxo de trabalho de solicitação de consentimento de administrador](configure-admin-consent-workflow.md) integrado ou por seus próprios processos de suporte. Consulte [cinco etapas para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md) para obter mais detalhes.

## <a name="configure-user-consent-to-applications"></a>Configurar consentimento do usuário para aplicativos
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Desabilitar ou habilitar o consentimento do usuário no portal do Azure

Você pode usar o portal do Azure para desabilitar ou habilitar a capacidade dos usuários de dar consentimento a aplicativos que acessam os dados da sua organização:

1. Entre no [portal do Azure](https://portal.azure.com) como um [administrador global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selecione **Azure Active Directory**, em seguida, **aplicativos empresariais**e **configurações do usuário**.
3. Habilitar ou desabilitar o consentimento do usuário com o controle rotulado **os usuários podem dar consentimento a aplicativos que acessam dados da empresa em seu nome**.
4. Adicional Configure o [fluxo de trabalho de solicitação de consentimento do administrador](configure-admin-consent-workflow.md) para garantir que os usuários que não têm permissão para dar consentimento a um aplicativo possam solicitar aprovação.

> [!TIP]
> Para permitir que os usuários solicitem uma revisão do administrador de um aplicativo ao qual o usuário não tenha permissão para consentir (por exemplo, porque o consentimento do usuário foi desabilitado ou porque o aplicativo está solicitando permissões que o usuário não tem permissão para conceder), considere [Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Desabilitar ou habilitar o consentimento do usuário usando o PowerShell

Você pode usar o módulo do Azure AD PowerShell v1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) para habilitar ou desabilitar a capacidade dos usuários de dar consentimento aos aplicativos que acessam os dados da sua organização.

1. Entre na sua organização executando este cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Verifique se o consentimento do usuário está habilitado executando este cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Habilitar ou desabilitar o consentimento do usuário. Por exemplo, para desabilitar o consentimento do usuário, execute este cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurar o consentimento do proprietário do grupo para aplicativos que acessam dados do grupo

> [!IMPORTANT]
> As informações a seguir são para um recurso futuro que permitirá que os proprietários do grupo conceda acesso aos aplicativos aos dados de seus grupos. Quando esse recurso for liberado, ele será habilitado por padrão. Embora esse recurso ainda não seja lançado amplamente, você pode usar essas instruções para desabilitar a capacidade antecipada de seu lançamento.

Os proprietários do grupo podem autorizar aplicativos (por exemplo, aplicativos publicados por fornecedores terceirizados) para acessar os dados da sua organização associados a um grupo. Por exemplo, um proprietário de equipe (que é o proprietário do grupo do Office 365 para a equipe) pode permitir que um aplicativo Leia todas as mensagens de equipes na equipe ou liste o perfil básico dos membros de um grupo.

> [!NOTE]
> Independentemente dessa configuração, um proprietário de grupo sempre é permitido para adicionar outros usuários ou aplicativos diretamente como proprietários de um grupo.

### <a name="configure-group-owner-consent-using-powershell"></a>Configurar o consentimento do proprietário do grupo usando o PowerShell

Você pode usar o módulo de visualização do PowerShell do Azure AD ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) para habilitar ou desabilitar a capacidade dos proprietários do grupo de consentir com os aplicativos que acessam os dados da sua organização para os grupos que eles possuem.

1. Verifique se você está usando o módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (essa etapa será importante se você tiver instalado o módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) e o módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Conecte-se ao PowerShell do Azure AD.

   ```powershell
   Connect-AzureAD
   ```

3. Recupere o valor atual para as configurações de *política de autorização* configurações de diretório em seu locatário. Isso requer a verificação se as configurações de diretório para esse recurso foram criadas e, se não, usando os valores do modelo de configurações de diretório correspondente.

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

4. Entenda os valores de configuração. Há dois valores de configurações que definem quais usuários podem permitir que um aplicativo acesse os dados do seu grupo:

    | Definição       | Tipo         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleano |  Sinalizador que indica se os proprietários de grupos têm permissão para conceder permissões específicas do grupo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Se _EnableGroupSpecificConsent_ for definido como "true" e esse valor for definido como a ID de objeto de um grupo, os membros do grupo identificado serão autorizados a conceder permissões específicas de grupo aos grupos que eles possuem. |

5. Atualize os valores das configurações para a configuração desejada:

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

6. Salvar configurações.

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

[Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)

[Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)

[Permissões e consentimento na plataforma Microsoft Identity](../develop/active-directory-v2-scopes.md)

[Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
