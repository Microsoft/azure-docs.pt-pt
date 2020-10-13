---
title: Configurar classificações de permissão com Azure AD
description: Saiba como gerir classificações de permissões delegadas.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0add5001e2e62e26d448b06927210c14f17729f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804345"
---
# <a name="configure-permission-classifications"></a>Configurar as classificações de permissão

As classificações de permissão permitem identificar o impacto que as diferentes permissões têm de acordo com as políticas da sua organização e avaliações de risco. Por exemplo, pode utilizar classificações de permissão em políticas de consentimento para identificar o conjunto de permissões a que os utilizadores estão autorizados a consentir.

## <a name="manage-permission-classifications"></a>Gerir classificações de permissões

Atualmente, apenas a classificação de permissão de "baixo impacto" é suportada. Apenas permissões delegadas que não exijam consentimento administrativo podem ser classificadas como "Baixo impacto".

> [!TIP]
> As permissões mínimas necessárias para fazer o signo básico são `openid` , e , que são todas `profile` `email` `User.Read` `offline_access` permissões delegadas no Gráfico da Microsoft. Com estas permissões, uma aplicação pode ler todos os detalhes do perfil completo do utilizador inscrito e pode manter este acesso mesmo quando o utilizador já não estiver a utilizar a app.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para classificar as permissões utilizando o portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
1. Selecione **Azure Ative Directory**  >  **Enterprise Aplicações**  >  **Consentimento e permissões**  >  **Classificações de permissão**.
1. Escolha **Permissões de adicionar** para classificar outra permissão como "Baixo impacto".
1. Selecione a API e, em seguida, selecione as permissões delegadas.

Neste exemplo, classificamos o conjunto mínimo de permissão necessária para uma única inscrição:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Classificações de permissão":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o mais recente módulo de pré-visualização Azure AD PowerShell, [AzureADPreview,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)para classificar permissões. As classificações de permissão são configuradas no objeto **ServicePrincipal** da API que publica as permissões.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Listar as classificações de permissão atuais para uma API

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

#### <a name="classify-a-permission-as-low-impact"></a>Classificar uma permissão como "Baixo impacto"

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

#### <a name="remove-a-delegated-permission-classification"></a>Remover uma classificação de permissão delegada

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

---

## <a name="next-steps"></a>Passos seguintes

Para saber mais:

* [Configurar as definições de consentimento do utilizador](configure-user-consent.md)
* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Saiba como gerir o consentimento das candidaturas e avaliar pedidos de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
