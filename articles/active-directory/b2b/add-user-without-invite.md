---
title: Adicione hóspedes B2B sem um link de convite ou e-mail - Azure AD
description: Pode permitir que um utilizador convidado adicione outros utilizadores ao seu Anúncio Azure sem resgatar um convite em colaboração Azure Ative Directory B2B.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050897"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Adicione utilizadores convidados de colaboração B2B sem um link de convite ou e-mail

Pode agora convidar os utilizadores convidados enviando um link direto para uma aplicação partilhada. Com este método, os utilizadores convidados já não precisam de usar o e-mail de convite, exceto em alguns casos especiais. Um utilizador convidado clica no link da aplicação, analisa e aceita os termos de privacidade e, em seguida, acede perfeitamente à aplicação. Para mais informações, consulte a redenção do convite de [colaboração B2B.](redemption-experience.md)   

Antes de este novo método estar disponível, você poderia convidar os utilizadores convidados sem precisar do e-mail de convite adicionando um convidado (da sua organização ou de uma organização parceira) para o papel de diretório **convidado** convidado, e, em seguida, ter o convidado adicionar utilizadores convidados ao diretório, grupos ou aplicações através da UI ou através da PowerShell. (Se utilizar o PowerShell, pode suprimir completamente o e-mail de convite). Por exemplo:

1. Um utilizador da organização anfitriã (por exemplo, WoodGrove) convida um utilizador Sam@litware.comda organização parceira (por exemplo, ) como Guest.
2. O administrador da organização anfitriã [estabelece políticas](delegate-invitations.md) que permitem à Sam identificar e adicionar outros utilizadores da organização parceira (Litware). (Sam deve ser adicionado ao papel de **convidado** convidado.)
3. Agora, Sam pode adicionar outros utilizadores da Litware ao diretório WoodGrove, grupos ou aplicações sem precisar de convites para ser resgatado. Se o Sam tiver os privilégios de enumeração apropriados em Litware, isso acontece automaticamente.
 
Este método original ainda funciona. No entanto, há uma pequena diferença de comportamento. Se utilizar o PowerShell, notará que uma conta de hóspedes convidada tem agora um estatuto **de Aceitação Pendente** em vez de mostrar imediatamente **"Accepted "**. Embora o estado esteja pendente, o utilizador convidado ainda pode iniciar sessão e aceder à aplicação sem clicar num link de convite por e-mail. O estatuto pendente significa que o utilizador ainda não passou pela experiência de [consentimento,](redemption-experience.md#consent-experience-for-the-guest)onde aceita os termos de privacidade da organização convidativa. O utilizador convidado vê este ecrã de consentimento quando iniciar sessão pela primeira vez. 

Se convidar um utilizador para o diretório, o utilizador convidado deve aceder diretamente https://portal.azure.com/ao url do portal Azure específico do arrendatário de recursos (como o inquilino de *recursos*.onmicrosoft.com) para ver e concordar com os termos de privacidade.

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration](delegate-invitations.md) (Delegar convites para colaboração do Azure Active Directory B2B)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)

