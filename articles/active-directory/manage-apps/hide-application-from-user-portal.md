---
title: Ocultar um aplicativo da experiência do usuário no Azure AD
description: Como ocultar um aplicativo da experiência do usuário em painéis de acesso Azure Active Directory ou em iniciadores do Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970640"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicativos de usuários finais no Azure Active Directory

Instruções sobre como ocultar aplicativos do painel myapps ou do iniciador do Office 365 dos usuários finais. Quando um aplicativo está oculto, os usuários ainda têm permissões para o aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios de administrador do aplicativo são necessários para ocultar um aplicativo do painel myapps e do iniciador do Office 365.

São necessários privilégios de administrador global para ocultar todos os aplicativos do Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar um aplicativo do usuário final
Use as etapas a seguir para ocultar um aplicativo do painel myapps e do iniciador de aplicativos do Office 365.

1.  Entre no [portal do Azure](https://portal.azure.com) como o administrador global do seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **aplicativos empresariais**. A folha **aplicativos empresariais – todos os aplicativos** é aberta.
4.  Em **tipo de aplicativo**, selecione **aplicativos empresariais**, se ainda não estiver selecionado.
5.  Pesquise o aplicativo que você deseja ocultar e clique no aplicativo.  A visão geral do aplicativo é aberta.
6.  Clique em **Propriedades**. 
7.  Para a pergunta **visível para os usuários?** , clique em **não**.
8.  Clique em **Guardar**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usar o PowerShell do Azure AD para ocultar um aplicativo

Para ocultar um aplicativo do painel myapps, você pode adicionar manualmente a marca HideApp à entidade de serviço para o aplicativo. Execute os comandos do [PowerShell do AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) a seguir para definir a propriedade visível do aplicativo **para usuários?** como **não**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ocultar aplicativos do Office 365 do painel myapps

Use as etapas a seguir para ocultar todos os aplicativos do Office 365 do painel myapps. Os aplicativos ainda estão visíveis no portal do Office 365.

1.  Entre no [portal do Azure](https://portal.azure.com) como um administrador global para seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Definições do utilizador**.
4.  Em **aplicativos empresariais**, clique em **gerenciar como os usuários finais iniciam e exibem seus aplicativos.**
5.  Para **os usuários só podem ver aplicativos do office 365 no portal do office 365**, clique em **Sim**.
6.  Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou o logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)

