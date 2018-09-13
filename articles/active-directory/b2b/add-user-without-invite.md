---
title: Adicionar utilizadores de colaboração B2B ao Azure Active Directory sem um convite | Documentos da Microsoft
description: Pode permitir que um utilizador convidado adicione outros utilizadores convidados para o Azure AD sem resgatar um convite em colaboração do Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3d18f3751be1ce2b00d83ecae0eebb27f240b53c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647170"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adicionar utilizadores de convidados de colaboração B2B sem convite

Agora pode convidar utilizadores convidados pelo envio de uma ligação direta para uma aplicação partilhada. Com esse método, os utilizadores convidados já não tem de utilizar o e-mail de convite, exceto em alguns casos especiais. Um utilizador convidado clica na ligação de aplicação, analisa e aceita os termos de privacidade e, em seguida, acede diretamente à aplicação. Para obter mais informações, consulte [resgate de convite de colaboração do B2B](redemption-experience.md).   

Antes deste novo método estava disponível, pode convidar utilizadores convidados sem a necessidade do e-mail de convite, adicionando um autor do convite (da sua organização ou de uma organização de parceiro) para o **autor de convites** função de diretório e, em seguida, fazer com que o autor do convite adicionar utilizadores convidados para o diretório, grupos ou aplicações através da IU ou através do PowerShell. (Se utilizar o PowerShell, é possível suprimir o e-mail de convite completamente). Por exemplo:

1. Um utilizador na organização do host (por exemplo, o WoodGrove) convida um utilizador da organização do parceiro (por exemplo, Sam@litware.com) como convidado.
2. O administrador da organização de anfitrião [configura as políticas](delegate-invitations.md) que permitem Sam identificar e adicione outros utilizadores da organização parceira (Litware). (Sam tem de ser adicionado para o **autor de convites** função.)
3. Agora, Sam pode adicionar outros utilizadores de Litware para o diretório de WoodGrove, grupos ou aplicações sem a necessidade de convites para ser resgatado. Se o Sam tem os privilégios apropriados enumeração Litware, isso acontece automaticamente.
 
Este método original ainda funciona. No entanto, há uma pequena diferença no comportamento. Se utilizar o PowerShell, observará que uma conta de convidado agora tem um **PendingAcceptance** Estado em vez de mostrar imediatamente **aceites**. Embora o estado seja pendente, o utilizador convidado ainda pode iniciar sessão e aceder à aplicação sem clicar num link de convite de e-mail. O estado pendente significa que o utilizador não ainda passou pela [experiência de consentimento](redemption-experience.md#privacy-policy-agreement), em que aceitem os termos de privacidade da organização convite. O utilizador convidado verá este ecrã de consentimento quando iniciam sessão pela primeira vez. 

Se convidar um utilizador no diretório, o utilizador convidado tem de aceder ao recurso específico de inquilino do portal do Azure diretamente a URL (por exemplo, https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) para ver e aceitar os termos de privacidade.

### <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Delegar convites para colaboração do Azure Active Directory B2B](delegate-invitations.md)
- [Como é que os operadores de informações adicionar utilizadores de colaboração B2B?](add-users-information-worker.md)

