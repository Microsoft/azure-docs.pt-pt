---
title: Microsoft 365 partilha externa e colaboração B2B - Azure AD
description: Discute a partilha de recursos com parceiros externos utilizando a colaboração do Microsoft 365 e do Azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0950c92b5a7bb1e782537aa46acad2e949e565c7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441798"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Partilha externa microsoft 365 e colaboração B2B do Azure Ative (Azure AD)

Tanto na colaboração AD B2B do Azure como na partilha externa do Microsoft 365 (OneDrive, SharePoint Online, Grupos Unificados, etc.), os utilizadores externos são autenticados utilizando o Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Como é que o Azure AD B2B difere da partilha externa no SharePoint Online?

A OneDrive/SharePoint Online tem um gestor de convites separado. O suporte à partilha externa no OneDrive/SharePoint Online começou antes da Azure AD desenvolver o seu suporte. Ao longo do tempo, a partilha externa OneDrive/SharePoint Online tem acumulado várias funcionalidades e muitos milhões de utilizadores que usam o padrão de partilha incorporado do produto. No entanto, existem algumas diferenças subtis entre como funciona a partilha externa OneDrive/SharePoint Online e como funciona a colaboração AZure AD B2B. Pode saber mais sobre a partilha externa OneDrive/SharePoint Online na [visão geral de partilha externa.](/sharepoint/external-sharing-overview) O processo geralmente difere do Azure AD B2B desta forma:

- O OneDrive/SharePoint Online adiciona os utilizadores ao diretório depois de os utilizadores terem resgatado os seus convites. Então, antes da redenção, não se vê o utilizador no portal AD AZure. Se outro site convidar um utilizador entretanto, é gerado um novo convite. No entanto, quando utiliza a colaboração Azure AD B2B, os utilizadores são adicionados imediatamente a convite para que apareçam em todo o lado.

- A experiência de resgate no OneDrive/SharePoint Online é diferente da experiência na colaboração Azure AD B2B. Depois de um utilizador resgatar um convite, as experiências são semelhantes.

- A colaboração Azure AD B2B os utilizadores convidados podem ser escolhidos a partir de caixas de diálogo de partilha OneDrive/SharePoint Online. Os utilizadores convidados oneDrive/SharePoint online também aparecem no Azure AD depois de resgatarem os seus convites.

- Os requisitos de licenciamento diferem. Por cada licença Azure AD paga, pode permitir que até 5 utilizadores convidados acedam às suas funcionalidades AZure AD pagas. Para saber mais sobre o licenciamento, consulte [o licenciamento Azure AD B2B](./external-identities-pricing.md) e ["O que é um utilizador externo?" na visão geral de partilha externa do SharePoint Online](/sharepoint/external-sharing-overview#what-happens-when-users-share).

Para gerir a partilha externa no OneDrive/SharePoint Online com a colaboração Azure AD B2B, defina a definição de partilha externa OneDrive/SharePoint Online para **permitir a partilha apenas com os utilizadores externos que já existem no diretório da sua organização.** Os utilizadores podem ir a sites partilhados externamente e escolher entre colaboradores externos que o administrador acrescentou. O administrador pode adicionar os colaboradores externos através do convite de colaboração B2B APIs.


![A definição de partilha externa OneDrive/SharePoint](media/o365-external-user/odsp-sharing-setting.png)

Depois de permitir a partilha externa, a capacidade de procurar utilizadores convidados existentes no picker de pessoas SharePoint Online (SPO) é OFF por padrão para corresponder ao comportamento do legado.

Pode ativar esta funcionalidade utilizando a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível do arrendatário e da recolha do site. Pode definir a funcionalidade utilizando os Set-SPOTenant e Set-SPOSite cmdlets, que permitem aos membros pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do arrendatário não afetam os sítios SPO já previstos.

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Adicionar um utilizador de colaboração B2B a uma função](add-guest-to-role.md)
* [Convites de colaboração do delegado B2B](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
* [Resolução de problemas Azure Ative Directy B2B colaboração](troubleshoot.md)