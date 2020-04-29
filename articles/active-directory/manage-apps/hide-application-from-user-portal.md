---
title: Ocultar uma aplicação da experiência do utilizador em Azure AD
description: Como ocultar uma aplicação da experiência do utilizador em painéis de acesso ao Diretório Ativo Azure ou lançadores do Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295056"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicações de utilizadores finais no Diretório Ativo do Azure

Instruções para ocultar aplicações do painel MyApps dos utilizadores finais ou do lançador office 365. Quando uma aplicação é ocultada, os utilizadores ainda têm permissões para a aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios de administrador de aplicação são necessários para ocultar uma aplicação do painel MyApps e do lançador office 365.

Os privilégios globais do administrador são necessários para esconder todas as aplicações do Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar uma aplicação do utilizador final
Utilize os seguintes passos para ocultar uma aplicação do painel MyApps e do lançador de aplicações office 365.

1.  Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global para o seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **aplicações Enterprise**. As **aplicações da Enterprise - Todas as aplicações** da lâmina abrem.
4.  Em **Tipo de Aplicação,** selecione **Aplicações Empresariais,** se ainda não estiver selecionado.
5.  Procure a aplicação que pretende ocultar e clique na aplicação.  A visão geral da aplicação abre.
6.  Clique em **Propriedades**. 
7.  Para o Visível para os **No** **utilizadores?**
8.  Clique em **Guardar**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Use a Azure AD PowerShell para ocultar uma aplicação

Para ocultar uma aplicação do painel MyApps, pode adicionar manualmente a etiqueta HideApp ao principal de serviço da aplicação. Executar os seguintes comandos [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) para definir a **No**propriedade Visível para utilizadores da **aplicação?** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ocultar escritório 365 aplicações do painel MyApps

Utilize os seguintes passos para ocultar todas as aplicações do Office 365 do painel MyApps. As candidaturas ainda são visíveis no portal do Office 365.

1.  Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global para o seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Definições do utilizador**.
4.  No âmbito **das aplicações da Enterprise,** clique em **Gerir como os utilizadores finais lançam e vêem as suas aplicações.**
5.  Para **utilizadores só podem ver as aplicações do Office 365 no portal do Office 365**, clique **sim**.
6.  Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes
* [Veja todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de uma aplicação empresarial](change-name-or-logo-portal.md)

