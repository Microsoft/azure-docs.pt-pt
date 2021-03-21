---
title: Configure o consentimento do proprietário do grupo para apps que acedam aos dados do grupo usando Azure AD
description: Saiba se os proprietários de grupos e equipas podem consentir com aplicações que terão acesso aos dados do grupo ou da equipa.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8d8604a1dd54ed819bb9e27c46d61a46466bf3da
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548806"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configure o consentimento do proprietário do grupo para apps que acedam aos dados do grupo

Os proprietários de grupos e equipas podem autorizar aplicações, como aplicações publicadas por fornecedores de terceiros, a aceder aos dados da sua organização associados a um grupo. Por exemplo, um dono de equipa em Microsoft Teams pode permitir que uma aplicação leia todas as mensagens de Equipas na equipa ou liste o perfil básico dos membros de um grupo. Consulte [o consentimento específico do recurso nas Equipas da Microsoft](/microsoftteams/resource-specific-consent) para saber mais.

## <a name="manage-group-owner-consent-to-apps"></a>Gerir o consentimento do proprietário do grupo para apps

Pode configurar quais os utilizadores que estão autorizados a consentir em aplicações que acedam aos dados dos seus grupos ou equipas, ou pode desativar isso para todos os utilizadores.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas medidas para gerir o consentimento do proprietário do grupo para aplicações que acedam aos dados do grupo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../roles/permissions-reference.md#global-administrator)
2. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Consent and permissions** User consent  >  **settings**.
3. Sob **o consentimento do proprietário do Grupo para aplicações que acedam a dados** selecione a opção que gostaria de ativar.
4. **Selecione Guardar** para guardar as suas definições.

Neste exemplo, todos os proprietários do grupo estão autorizados a consentir que as aplicações acedam aos dados dos seus grupos:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Definições de consentimento do proprietário do grupo":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)para permitir ou desativar a capacidade dos proprietários do grupo de consentirem com as aplicações que acedem aos dados da sua organização para os grupos que possuem.

1. Certifique-se de que está a utilizar o módulo [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Este passo é importante se tiver instalado o módulo [AzureAD](/powershell/module/azuread/) e o módulo [AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

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

    | Definição       | Tipo         | Description  |
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

---

## <a name="next-steps"></a>Passos seguintes

Para saber mais:

* [Configurar as definições de consentimento do utilizador](configure-user-consent.md)
* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento das candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no Microsoft Q&A ](/answers/topics/azure-active-directory.html)