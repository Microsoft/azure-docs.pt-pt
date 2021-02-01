---
title: Gerir políticas de consentimento de aplicativos em Azure AD
description: Saiba como gerir políticas de consentimento de aplicações incorporadas e personalizadas para controlar quando o consentimento pode ser concedido.
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
ms.openlocfilehash: 62a8b48d6b33a92b62bc4c3634794190585615b7
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222033"
---
# <a name="manage-app-consent-policies"></a>Gerir as políticas de consentimento da aplicação

Com o Azure AD PowerShell, pode ver e gerir políticas de consentimento de aplicações.

Uma política de consentimento de aplicações consiste em conjuntos de condições "inclui" um ou mais conjuntos de condições e conjuntos de condições zero ou mais "exclui". Para que um evento seja considerado numa política de consentimento de aplicações, deve corresponder *a pelo menos* um conjunto de condições "inclui" e não deve corresponder a *qualquer* conjunto de condições "excluis".

Cada conjunto de condições consiste em várias condições. Para que um evento corresponda a um conjunto de condições, *todas as* condições do conjunto de condições devem ser satisfeitas.

As políticas de consentimento da aplicação onde o ID começa com "microsoft-" são políticas incorporadas. Algumas destas políticas incorporadas são utilizadas em funções de diretório incorporado existentes. Por exemplo, a política de consentimento da `microsoft-application-admin` aplicação descreve as condições em que as funções de Administrador de Aplicação e Administrador de Aplicação em Nuvem são autorizadas a conceder o consentimento administrativo do inquilino. As políticas incorporadas podem ser usadas em funções de diretório personalizado e para configurar as definições de consentimento do utilizador, mas não podem ser editadas ou eliminadas.

## <a name="pre-requisites"></a>Pré-requisitos

1. Certifique-se de que está a utilizar o módulo [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Este passo é importante se tiver instalado o módulo [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) e o módulo [AzureADPreview).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Ligue-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Listar as políticas de consentimento de aplicações existentes

É uma boa ideia começar por se familiarizar com as políticas de consentimento de aplicações existentes na sua organização:

1. Listar todas as políticas de consentimento da aplicação:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Veja os conjuntos de condições "inclui" de uma política:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Ver os conjuntos de condições "exclui":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Crie uma política de consentimento de aplicativos personalizado

Siga estes passos para criar uma política de consentimento de aplicações personalizadas:

1. Crie uma nova política de consentimento de aplicações vazia.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Adicione conjuntos de condições "inclui".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Repita este passo para adicionar conjuntos adicionais de condição de "incluir".

1. Opcionalmente, adicione conjuntos de condições "exclui".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Repita este passo para adicionar conjuntos adicionais de condição de "excluir".

Uma vez criada a política de consentimento da aplicação, pode [permitir o consentimento do utilizador](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) sujeito a esta política.

## <a name="delete-a-custom-app-consent-policy"></a>Excluir uma política de consentimento de aplicações personalizadas

1. O seguinte mostra como pode eliminar uma política de consentimento de aplicações personalizadas. **Esta ação não pode ser anulada.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> As políticas de consentimento de aplicações eliminadas não podem ser restauradas. Se eliminar acidentalmente uma política de consentimento de aplicações personalizadas, terá de recriar a política.

---

### <a name="supported-conditions"></a>Condições apoiadas

A tabela seguinte fornece a lista de condições suportadas para políticas de consentimento de aplicações.

| Condição | Description|
|:---------------|:----------|
| Classificação de Permissões | A classificação de [permissão](configure-permission-classifications.md) para a permissão concedida, ou "todos" para corresponder a qualquer classificação de permissão (incluindo permissões que não são classificadas). O padrão é "tudo". |
| Tipo de permissão | O tipo de permissão que está a ser concedida. Utilize "aplicação" para permissões de candidatura (por exemplo.app funções) ou "delegada" para permissões delegadas. <br><br>**Nota:** O valor "delegadoUserConsentable" indica permissões delegadas que não foram configuradas pelo editor da API para exigir o consentimento administrativo — este valor pode ser usado em políticas de concessão de permissões incorporadas, mas não pode ser utilizado em políticas de concessão de permissão personalizadas. Obrigatório. |
| Aplicação de Recursos | O **AppId** da aplicação de recursos (por exemplo, a API) para o qual está a ser concedida uma autorização, ou "qualquer" para corresponder a qualquer aplicação de recursos ou API. O padrão é "qualquer". |
| Permissões | A lista de identificações de permissão para as permissões específicas a combinar, ou uma lista com o valor único "todos" para combinar com qualquer permissão. O padrão é o único valor "todos". <ul><li>Os IDs de permissão delegado podem ser encontrados na propriedade **OAuth2Permissions** do objeto ServicePrincipal da API.</li><li>Os IDs de permissão de aplicação podem ser encontrados na propriedade **appRoles** do objeto ServicePrincipal da API.</li></ol> |
| ClientApplicationIds | Uma lista de valores de **AppId** para as aplicações do cliente a combinar, ou uma lista com o valor único "todos" para corresponder a qualquer aplicação do cliente. O padrão é o único valor "todos". |
| ClientApplicationTenantIds | Uma lista de IDs de inquilino do Azure Ative Directory em que o pedido do cliente está registado, ou uma lista com o valor único "todos" para corresponder com aplicações de clientes registadas em qualquer inquilino. O padrão é o único valor "todos". |
| ClientApplicationPublisherIds | Uma lista de IDs da Microsoft Partner Network (MPN) para [editores verificados](../develop/publisher-verification-overview.md) da aplicação do cliente, ou uma lista com o valor único "todos" para combinar com aplicações de clientes de qualquer editor. O padrão é o único valor "todos". |
| Aplicações clientesFromVerifiedPublisherOnly | Definido `$true` para apenas combinar em aplicações de cliente com uma [editora verificada](../develop/publisher-verification-overview.md). Definido `$false` para corresponder em qualquer app do cliente, mesmo que não tenha um editor verificado. A predefinição é `$false`. |

## <a name="next-steps"></a>Passos seguintes

Para saber mais:

* [Configurar as definições de consentimento do utilizador](configure-user-consent.md)
* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento das candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no Microsoft Q&A](https://docs.microsoft.com/answers/products/)