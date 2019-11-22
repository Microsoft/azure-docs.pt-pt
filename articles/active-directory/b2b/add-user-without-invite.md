---
title: Adicionar convidados B2B sem um link de convite ou email-Azure AD
description: Você pode permitir que um usuário convidado adicione outros usuários convidados ao seu Azure AD sem resgatar um convite na colaboração Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9efbb941e589cb8e4cf56ee06a697a1557a3cc89
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74268934"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Adicionar usuários convidados de colaboração B2B sem um link ou email de convite

Agora você pode convidar usuários convidados enviando um link direto para um aplicativo compartilhado. Com esse método, os usuários convidados não precisam mais usar o email de convite, exceto em alguns casos especiais. Um usuário convidado clica no link do aplicativo, revisa e aceita os termos de privacidade e, em seguida, acessa o aplicativo diretamente. Para obter mais informações, consulte [resgate de convite de colaboração B2B](redemption-experience.md).   

Antes que esse novo método estivesse disponível, você poderia convidar os usuários convidados sem exigir o email de convite adicionando um emissor (de sua organização ou de uma organização parceira) à função de diretório do **convite do convidado** e, em seguida, fazendo com que o emissor adicione usuários convidados ao diretório, grupos ou aplicativos por meio da interface do usuário ou por meio do PowerShell. (Se estiver usando o PowerShell, você poderá suprimir o email de convite completamente). Por exemplo:

1. Um usuário na organização host (por exemplo, WoodGrove) convida um usuário da organização parceira (por exemplo, Sam@litware.com) como convidado.
2. O administrador na organização de hosts [configura políticas](delegate-invitations.md) que permitem ao Sam identificar e adicionar outros usuários da organização parceira (Litware). (Sam deve ser adicionado à função de emissor de **convidado** .)
3. Agora, o Sam pode adicionar outros usuários de Litware ao diretório, grupos ou aplicativos do WoodGrove sem precisar que os convites sejam resgatados. Se Sam tiver os privilégios de enumeração apropriados no Litware, isso ocorrerá automaticamente.
 
Esse método original ainda funciona. No entanto, há uma pequena diferença no comportamento. Se você usar o PowerShell, observará que uma conta convidado convidada agora tem um status de **PendingAcceptance** em vez de mostrar imediatamente o **aceito**. Embora o status esteja pendente, o usuário convidado ainda pode entrar e acessar o aplicativo sem clicar em um link de convite por email. O status pendente significa que o usuário ainda não passou pela experiência de [consentimento](redemption-experience.md#consent-experience-for-the-guest), na qual eles aceitam os termos de privacidade da organização que está convidando. O usuário convidado vê essa tela de consentimento quando entra pela primeira vez. 

Se você convidar um usuário para o diretório, o usuário convidado deverá acessar a URL de portal do Azure específica do locatário de recursos diretamente (como https://portal.azure.com/*resourcetenant*. onmicrosoft.com) para exibir e concordar com os termos de privacidade.

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Resgate de convite para colaboração B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration](delegate-invitations.md) (Delegar convites para colaboração do Azure Active Directory B2B)
- [Como os operadores de informações adicionam usuários de colaboração B2B?](add-users-information-worker.md)

