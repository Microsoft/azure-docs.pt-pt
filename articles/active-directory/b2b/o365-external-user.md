---
title: Office 365 partilha externa e colaboração B2B - Azure AD
description: Discute a partilha de recursos com parceiros externos utilizando a colaboração O365 e Azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272275"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 partilha externa e colaboração Azure Ative Directory B2B

A partilha externa no Office 365 (OneDrive, SharePoint Online, Grupos Unificados, etc.) e na colaboração Azure Ative Directory (Azure AD) B2B são tecnicamente a mesma coisa. Toda a partilha externa (exceto OneDrive/SharePoint Online), incluindo convidados em Grupos Office 365, já utiliza o convite de colaboração Azure AD B2B PARA partilha.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Como é que o Azure AD B2B difere da partilha externa no SharePoint Online?

OneDrive/SharePoint Online tem um gestor de convites separado. O suporte para partilha externa no OneDrive/SharePoint Online começou antes da Azure AD desenvolver o seu suporte. Ao longo do tempo, a partilha externa OneDrive/SharePoint Online tem acumulado várias funcionalidades e muitos milhões de utilizadores que usam o padrão de partilha incorporado do produto. No entanto, existem algumas diferenças subtis entre o funcionamento da partilha externa OneDrive/SharePoint Online e como funciona a colaboração Azure AD B2B. Pode saber mais sobre a partilha externa OneDrive/SharePoint Online na visão geral da [partilha externa](https://docs.microsoft.com/sharepoint/external-sharing-overview). O processo geralmente difere do Azure AD B2B desta forma:

- O OneDrive/SharePoint Online adiciona os utilizadores ao diretório depois de os utilizadores terem resgatado os seus convites. Então, antes da redenção, não se vê o utilizador no portal Azure AD. Se outro site convidar um utilizador entretanto, é gerado um novo convite. No entanto, quando se utiliza a colaboração Azure AD B2B, os utilizadores são adicionados imediatamente a convite para que apareçam em todo o lado.

- A experiência de resgate no OneDrive/SharePoint Online parece diferente da experiência na colaboração Azure AD B2B. Depois de um utilizador redimir um convite, as experiências são parecidas.

- Os utilizadores convidados de colaboração Azure AD B2B podem ser escolhidos a partir de caixas de diálogo de partilha OneDrive/SharePoint Online. Os utilizadores convidados do OneDrive/SharePoint Online também aparecem no Azure AD depois de resgatarem os seus convites.

- Os requisitos de licenciamento diferem. Por cada licença Azure AD paga, pode permitir que até 5 utilizadores convidados acedam às suas funcionalidades de AD Azure pagas. Para saber mais sobre o licenciamento, consulte o [licenciamento Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) e ["O que é um utilizador externo?" na visão geral da partilha externa sharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Para gerir a partilha externa no OneDrive/SharePoint Online com a colaboração azure AD B2B, defina a definição de partilha externa OneDrive/SharePoint Online para **permitir a partilha apenas com os utilizadores externos que já existem no diretório da sua organização.** Os utilizadores podem ir a sites partilhados externamente e escolher entre colaboradores externos que o administrador acrescentou. O administrador pode adicionar os colaboradores externos através do convite de colaboração B2B APIs.


![A definição de partilha externa OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

Depois de permitir a partilha externa, a capacidade de procurar utilizadores convidados existentes no picker de pessoas SharePoint Online (SPO) é OFF por padrão para corresponder ao comportamento do legado.

Pode ativar esta funcionalidade utilizando a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível do inquilino e da recolha do site. Pode definir a funcionalidade utilizando os cmdlets Set-SPOTenant e Set-SPOSite, que permitem aos membros pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito dos inquilinos não afetam os sítios SPO já previstos.

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Adicionar um utilizador de colaboração B2B a um papel](add-guest-to-role.md)
* [Convites de colaboração B2B delegados](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
* [Resolução de problemas Azure Ative Directory B2B colaboração](troubleshoot.md)
