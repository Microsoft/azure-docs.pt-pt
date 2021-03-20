---
title: Adicione aos hóspedes B2B sem link de convite ou e-mail - Azure AD
description: Pode permitir que um utilizador convidado adicione outros utilizadores convidados ao seu AD Azure sem resgatar um convite na colaboração B2B do Azure Ative Directory.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909805"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Adicione utilizadores convidados de colaboração B2B sem um link de convite ou e-mail

Agora pode convidar os utilizadores convidados enviando um [link direto](redemption-experience.md#redemption-through-a-direct-link) para uma aplicação partilhada. Com este método, os utilizadores convidados deixaram de precisar de utilizar o e-mail de convite, exceto em alguns casos especiais. Um utilizador convidado clica no link da aplicação, analisa e aceita os termos de privacidade e, em seguida, acede perfeitamente à aplicação. Para mais informações, consulte [o pedido de colaboração B2B de resgate.](redemption-experience.md)

Antes deste novo método estar disponível, pode convidar os utilizadores convidados sem necessitar do e-mail de convite adicionando um convidado (da sua organização ou de uma organização parceira) ao papel de **diretório convidado convidado,** e, em seguida, ter o convidado adicionar utilizadores convidados ao diretório, grupos ou aplicações através da UI ou através do PowerShell. (Se utilizar o PowerShell, pode suprimir completamente o e-mail do convite). Por exemplo:

1. Um utilizador na organização anfitriã (por exemplo, WoodGrove) convida um utilizador da organização do parceiro (por exemplo, Sam@litware.com ) como Convidado.
2. O administrador da organização anfitriã [estabelece políticas](delegate-invitations.md) que permitem ao Sam identificar e adicionar outros utilizadores da organização parceira (Litware). (Sam deve ser adicionado ao papel **de convidado** convidado.)
3. Agora, a Sam pode adicionar outros utilizadores da Litware ao diretório, grupos ou aplicações woodGrove sem precisar de convites para serem resgatados. Se o Sam tiver os privilégios de enumeração adequados em Litware, acontece automaticamente.
 
Este método original ainda funciona. No entanto, há uma pequena diferença de comportamento. Se utilizar o PowerShell, notará que uma conta de hóspedes convidada tem agora um estado **de aceitação pendente** em vez de mostrar imediatamente **Aceito**. Apesar do estado estar pendente, o utilizador convidado ainda pode iniciar sômposição e aceder à aplicação sem clicar num link de convite por e-mail. O estado pendente significa que o utilizador ainda não passou pela experiência de [consentimento,](redemption-experience.md#consent-experience-for-the-guest)onde aceita os termos de privacidade da organização convidada. O utilizador convidado vê este ecrã de consentimento quando iniciar o séc. 

Se convidar um utilizador para o diretório, o utilizador convidado deve aceder diretamente ao URL do portal Azure específico do inquilino (como https://portal.azure.com/ *o recurso*(onmicrosoft.com) para visualizar e concordar com os termos de privacidade.

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration](delegate-invitations.md) (Delegar convites para colaboração do Azure Active Directory B2B)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)

