---
title: Compartilhamento externo do Office 365 e colaboração B2B – Azure AD
description: Discute o compartilhamento de recursos com parceiros externos usando o O365 e Azure Active Directory colaboração B2B.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272275"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory

O compartilhamento externo no Office 365 (OneDrive, SharePoint Online, grupos unificados etc.) e a colaboração B2B do Azure Active Directory (Azure AD) são tecnicamente a mesma coisa. Todo compartilhamento externo (exceto OneDrive/SharePoint Online), incluindo convidados em grupos do Office 365, já usa as APIs de convite de colaboração B2B do Azure AD para compartilhamento.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Como o B2B do Azure AD difere do compartilhamento externo no SharePoint Online?

O OneDrive/SharePoint Online tem um Gerenciador de convite separado. O suporte para compartilhamento externo no OneDrive/SharePoint Online foi iniciado antes do Azure AD ter desenvolvido seu suporte. Ao longo do tempo, o compartilhamento externo do OneDrive/SharePoint Online tem acumulado vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. No entanto, há algumas diferenças sutis entre o funcionamento do compartilhamento externo do OneDrive/SharePoint Online e como funciona a colaboração B2B do Azure AD. Você pode saber mais sobre o compartilhamento externo do OneDrive/SharePoint Online na [visão geral do compartilhamento externo](https://docs.microsoft.com/sharepoint/external-sharing-overview). O processo geralmente difere do B2B do Azure AD das seguintes maneiras:

- O OneDrive/SharePoint Online adiciona usuários ao diretório depois que os usuários resgataram seus convites. Portanto, antes do resgate, você não vê o usuário no portal do Azure AD. Se outro site convidar um usuário enquanto isso, um novo convite será gerado. No entanto, quando você usa a colaboração B2B do Azure AD, os usuários são adicionados imediatamente no convite para que sejam exibidos em qualquer lugar.

- A experiência de resgate no OneDrive/SharePoint Online é diferente da experiência na colaboração B2B do Azure AD. Depois que um usuário resgata um convite, as experiências são semelhantes.

- Os usuários convidados da colaboração B2B do Azure AD podem ser selecionados de caixas de diálogo de compartilhamento do OneDrive/SharePoint Online. Os usuários convidados do OneDrive/SharePoint Online também aparecem no Azure AD depois de resgatar seus convites.

- Os requisitos de licenciamento são diferentes. Para cada licença paga do Azure AD, você pode permitir que até 5 usuários convidados acessem seus recursos pagos do Azure AD. Para saber mais sobre licenciamento, confira [Licenciamento B2B do Azure ad](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) e ["o que é um usuário externo?" na visão geral de compartilhamento externo do SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Para gerenciar o compartilhamento externo no OneDrive/SharePoint Online com a colaboração B2B do Azure AD, defina a configuração de compartilhamento externo do OneDrive/SharePoint Online para **permitir o compartilhamento somente com os usuários externos que já existem no diretório da sua organização**. Os usuários podem ir para sites compartilhados externamente e escolher colaboradores externos que o administrador adicionou. O administrador pode adicionar os colaboradores externos por meio das APIs de convite de colaboração B2B.


![A configuração de compartilhamento externo do OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

Depois de habilitar o compartilhamento externo, a capacidade de Pesquisar usuários convidados existentes no seletor de pessoas do SharePoint Online (SPO) está desativada por padrão para corresponder ao comportamento herdado.

Você pode habilitar esse recurso usando a configuração ' ShowPeoplePickerSuggestionsForGuestUsers ' no nível do locatário e do conjunto de sites. Você pode definir o recurso usando os cmdlets Set-SPOTenant e Set-SPOSite, que permitem que os membros pesquisem todos os usuários convidados existentes no diretório. As alterações no escopo do locatário não afetam os sites SPO já provisionados.

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Adicionando um usuário de colaboração B2B a uma função](add-guest-to-role.md)
* [Delegar convites de colaboração B2B](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
* [Solução de problemas Azure Active Directory colaboração B2B](troubleshoot.md)
