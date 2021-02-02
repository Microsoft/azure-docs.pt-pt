---
title: Esconda uma aplicação Enterprise da experiência do utilizador no Azure AD
description: Como ocultar uma aplicação da Enterprise da experiência do utilizador em painéis de acesso Azure Ative Directory ou lançadores Microsoft 365.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258885"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicações da Empresa de utilizadores finais no Azure Ative Directory

Instruções para como ocultar aplicações do painel MyApps dos utilizadores finais ou do lançador Microsoft 365. Quando uma aplicação é ocultada, os utilizadores ainda têm permissões para a aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios do administrador de aplicações são necessários para ocultar uma aplicação do painel MyApps e do lançador Microsoft 365.

Os privilégios globais de administrador são necessários para ocultar todas as aplicações da Microsoft 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar uma aplicação do utilizador final
Use os seguintes passos para esconder uma aplicação do painel MyApps e do lançador de aplicações Microsoft 365.

1.  Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **aplicações da Enterprise**. As **aplicações da Enterprise - Todas as aplicações** abrem a lâmina.
4.  Sob **o Tipo de Aplicação,** selecione **Aplicações empresariais,** se ainda não estiver selecionado.
5.  Procure a aplicação que deseja ocultar e clique na aplicação.  A visão geral da candidatura abre.
6.  Clique em **Propriedades**. 
7.  Para a pergunta **Visível para os utilizadores,** clique em **Nº**.
8.  Clique em **Guardar**.

> [!NOTE]
> Estas instruções aplicam-se apenas às aplicações da Enterprise.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Use a Azure AD PowerShell para esconder uma aplicação

Para ocultar uma aplicação do painel MyApps, pode adicionar manualmente a etiqueta HideApp ao principal do serviço para a aplicação. Executar os seguintes comandos [AzureAD PowerShell](/powershell/module/azuread/#service_principals) para definir a propriedade **Visível para Os Utilizadores da aplicação?** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Esconda as aplicações microsoft 365 do painel MyApps

Utilize os seguintes passos para esconder todas as aplicações da Microsoft 365 do painel MyApps. As aplicações ainda estão visíveis no portal do Office 365.

1.  Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global para o seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Utilizadores**.
4.  Selecione **Definições do utilizador**.
5.  Nas **aplicações da Enterprise,** clique **em Gerir como os utilizadores finais lançam e vêem as suas aplicações.**
6.  Para **os utilizadores só podem ver as aplicações do Office 365 no portal Do Office 365**, clique em **Sim**.
7.  Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação da empresa](./assign-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de uma aplicação da empresa](./add-application-portal-configure.md)
