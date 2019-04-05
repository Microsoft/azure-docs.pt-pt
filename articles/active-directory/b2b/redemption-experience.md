---
title: Resgate de convite em colaboração B2B - Azure Active Directory | Documentos da Microsoft
description: Descreve a experiência do resgate de convite colaboração B2B do Azure AD para utilizadores finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e5a8d46f67553cc10bd9cdb31cf64511858948f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046500"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate de convite de colaboração do Azure Active Directory B2B

Colaborar com os utilizadores de organizações parceiras por meio da colaboração B2B do Azure Active Directory (Azure AD), pode convidar utilizadores convidados para aceder a aplicações partilhadas. Depois de um utilizador convidado é adicionado ao diretório através da interface de utilizador ou o utilizador é convidado através do PowerShell, os utilizadores convidados devem passar por um processo de consentimento da primeira vez em que estão a aceitar [termos de privacidade](#privacy-policy-agreement). Este processo ocorre em qualquer uma das seguintes formas:

- O autor de convites envia uma ligação direta para uma aplicação partilhada. Mesmo o convidado clica na ligação para iniciar sessão, aceita os termos de privacidade e perfeitamente acessa o recurso partilhado. (O utilizador convidado ainda recebe um e-mail de convite com um URL de resgate, mas que não sejam alguns casos especiais, já não seja necessário para utilizar o e-mail de convite).  
- O utilizador convidado recebe um e-mail de convite e clica o URL de resgate. Como parte do tempo de primeiro início de sessão, eles são-lhe pedidos para aceitar os termos de privacidade.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de uma ligação direta

Um autor de convite pode convidar um utilizador convidado ao enviar um [ligação direta para uma aplicação partilhada](../manage-apps/end-user-experiences.md#direct-sign-on-links). Para o utilizador convidado, a experiência de resgate é tão fácil quanto a iniciar sessão para a aplicação que foi partilhada com eles. Pode clicar numa ligação para a aplicação, reveja e aceite os termos de privacidade e, em seguida, acessar diretamente a aplicação. Na maioria dos casos, os utilizadores convidados não precisam mais clique um URL de resgate numa mensagem de e-mail de convite.

Se os utilizadores convidados por meio da interface do usuário, ou optar por enviar o e-mail de convite como parte da experiência de convite do PowerShell, o usuário convidado ainda recebe um e-mail de convite. Este e-mail é útil para os seguintes casos especiais:

- O utilizador não tem uma conta do Azure AD ou uma conta Microsoft (MSA). Neste caso, o utilizador tem de criar uma MSA antes de estas clicarem na ligação, ou podem utilizar o URL de resgate no e-mail de convite. O processo de resgate solicita automaticamente o utilizador crie uma MSA.
- Por vezes, o objeto de utilizador convidado não pode ter um endereço de e-mail devido a um conflito com um objeto de contato (por exemplo, um Outlook contacto objeto). Neste caso, o utilizador tem de clicar o URL de resgate no e-mail de convite.
- O utilizador pode iniciar sessão com um alias do endereço de e-mail que foi convidado. (É um endereço de e-mail adicionais associado a uma conta de e-mail de um alias.) Neste caso, o utilizador tem de clicar o URL de resgate no e-mail de convite.

Se estes casos especiais são importantes para a sua organização, recomendamos que Convide utilizadores através de métodos que ainda enviar o e-mail de convite. Além disso, se um utilizador não se enquadra em um desses casos especiais, eles ainda podem clicar no URL numa mensagem de e-mail de convite para obter acesso.

## <a name="redemption-through-the-invitation-email"></a>Resgate através de e-mail de convite

Se o convidado através de um método que envia um e-mail de convite, os utilizadores também podem resgatar um convite através do e-mail de convite. Um utilizador convidado pode clique no URL de resgate no e-mail e, em seguida, reveja e aceite os termos de privacidade. O processo é descrito mais detalhadamente aqui:

1.  Depois de a ser convidado, mesmo o convidado recebe um convite por e-mail é enviado a partir **Invitations Microsoft**.
2.  Mesmo o convidado seleciona **começar** no e-mail.
3.  Se o mesmo o convidado não tiver uma conta do Azure AD ou uma MSA, são-lhe pedidos para criar uma MSA.
4.  Mesmo o convidado é redirecionado para o **rever permissões** tela, onde pode rever a declaração de privacidade da organização de convite e aceite os termos.

## <a name="privacy-policy-agreement"></a>Contrato de política de privacidade

Depois de qualquer utilizador convidado inicia sessão aceder aos recursos numa organização parceira pela primeira vez, veem uma **rever permissões** ecrã. Aqui, que possa rever a declaração de privacidade da organização o convite. Um utilizador tem de aceitar a utilização das informações de acordo com as políticas de privacidade da organização de convite para continuar.

![Captura de ecrã que mostra as definições de utilizador no painel de acesso](media/redemption-experience/ConsentScreen.png) 

Para obter informações sobre como, como um administrador inquilino pode ligar a declaração de privacidade da sua organização, consulte [procedimentos: Adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Termos de utilização

Pode apresentar termos de utilização para o utilizador convidado durante o processo de resgate inicial ao utilizar os termos de AD do Azure da funcionalidade de utilização. No Azure Active Directory, pode aceder a esta funcionalidade sob **Manage** > **relações organizacionais** > **termos de utilização** ou em **Security** > **acesso condicional** > **termos de utilização**. Para obter detalhes, consulte [do Azure AD termos de funcionalidade utilização](../conditional-access/terms-of-use.md).

![Captura de ecrã que mostra os novos termos de utilização](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Adicionar utilizadores de colaboração do Azure Active Directory B2B no portal do Azure](add-users-administrator.md)
- [Como os operadores de informações adicionar utilizadores de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar utilizadores de colaboração do Azure Active Directory B2B com o PowerShell](customize-invitation-api.md#powershell)
- [Sair de uma organização como um utilizador convidado](leave-the-organization.md)
