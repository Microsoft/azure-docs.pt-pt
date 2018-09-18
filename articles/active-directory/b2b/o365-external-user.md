---
title: A partilha externa do Office 365 e a colaboração do Azure Active Directory B2B | Documentos da Microsoft
description: Aborda o compartilhamento de recursos com parceiros externos através da colaboração do Office 365 e Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fc789283e56d220f0068277544cb6494e265ee39
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981929"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>A partilha externa do Office 365 e a colaboração do Azure Active Directory B2B

A partilha do Office 365 (OneDrive, SharePoint Online, grupos unificados, etc.) e a colaboração B2B do Azure Active Directory (Azure AD) externa é tecnicamente a mesma coisa. Partilha externa de todas as (exceto o OneDrive/SharePoint Online), inclusive convidados em grupos do Office 365, já utiliza o convite de colaboração B2B do Azure AD APIs para a partilha.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B difere da partilha externa no SharePoint Online?

OneDrive/SharePoint Online, tem um Gerenciador de convite separado. Suporte para a partilha externa no OneDrive/SharePoint Online iniciadas antes do Azure AD desenvolveu seu suporte. Ao longo do tempo, partilha externa OneDrive/SharePoint Online tem acumulados vários recursos e muitos milhões de utilizadores que utilizam o produto no-incorporada do padrão de partilha. No entanto, existem algumas diferenças sutis entre como partilha externa OneDrive/SharePoint Online funciona e como funciona a colaboração B2B do Azure AD. Pode saber mais sobre a partilha externa OneDrive/SharePoint Online no [descrição geral da partilha externa](https://docs.microsoft.com/sharepoint/external-sharing-overview). Em geral, o processo é diferente do Azure AD B2B nas seguintes formas:

- OneDrive/SharePoint Online adiciona os utilizadores no diretório depois dos utilizadores tem resgatado os seus convites. Portanto, antes de resgate, não vê o utilizador no portal do Azure AD. Se outro site convida um usuário enquanto isso, é gerado um novo convite. No entanto, quando usa a colaboração B2B do Azure AD, são adicionados utilizadores imediatamente no convite para que elas aparecem em todos os lugares.

- A experiência de resgate no OneDrive/SharePoint Online é diferente da experiência em colaboração B2B do Azure AD. Depois de um utilizador redeems um convite, as experiências parecidos.

- Os utilizadores convidado de colaboração do Azure AD B2B podem ser selecionados a partir do OneDrive/SharePoint Online caixas de diálogo de partilha. Utilizadores do OneDrive/SharePoint Online convidado também aparecem no Azure AD depois de resgatar os seus convites.

- Os requisitos de licenciamento diferem. Para cada pagas licença do Azure AD, pode permitir que até 5 utilizadores convidados acessar seu paga funcionalidades do Azure AD. Para saber mais sobre o licenciamento, consulte [licenciamento do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) e ["O que é um utilizador externo?" no SharePoint Online externas de descrição geral da partilha](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

Para gerir a partilha externa no OneDrive/SharePoint Online com a colaboração B2B do Azure AD, defina o OneDrive/SharePoint Online externo de compartilhamento de definição para **permitir partilha apenas com os utilizadores externos que já existem na sua organização diretório**. Os utilizadores podem aceder a sites partilhados externamente e escolha entre mais de funcionários externos que o administrador tenha adicionado. O administrador pode adicionar os colaboradores externos por meio do convite de colaboração do B2B APIs.


![O OneDrive/SharePoint Online externas de definição de partilha](media/o365-external-user/odsp-sharing-setting.png)

Depois de ativar a partilha externa, a capacidade de pesquisar para utilizadores convidados existentes no selecionador de pessoas de SharePoint Online (SPO) está DESATIVADO por predefinição de acordo com o comportamento herdado.

Pode ativar esta funcionalidade, utilize a definição 'ShowPeoplePickerSuggestionsForGuestUsers' ao nível da coleção inquilino e o site. Pode definir a funcionalidade com os cmdlets Set-SPOTenant e Set-SPOSite, que permitem aos membros procurar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do inquilino não afetam os sites SPO já aprovisionados.

## <a name="next-steps"></a>Passos Seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [A adição de um utilizador de colaboração do B2B a uma função](add-guest-to-role.md)
* [Delegar convites de colaboração B2B](delegate-invitations.md)
* [Grupos dinâmicos e de colaboração B2B](use-dynamic-groups.md)
* [Resolução de problemas de colaboração do Azure Active Directory B2B](troubleshoot.md)
