---
title: Elementos do e-mail de convite B2B - Azure Ative Directory / Microsoft Docs
description: Modelo de e-mail de convite de colaboração Azure Ative Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03391ba5a82c128197c86ea6ed84389552fadb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439843"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos do e-mail de convite de colaboração B2B - Azure Ative Directory

Os e-mails de convite são um componente crítico para trazer parceiros a bordo como utilizadores de colaboração B2B em Azure AD. Embora não seja [necessário que envie um e-mail para convidar alguém que use a colaboração B2B](add-user-without-invite.md), ao fazê-lo dá ao utilizador toda a informação de que necessita para tomar uma decisão sobre se aceita o seu convite. Também lhes dá um link a que podem sempre se referir no futuro quando precisam de voltar aos seus recursos.

![Screenshot mostrando o e-mail de convite B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Este novo modelo de e-mail ainda está sendo lançado para todos os inquilinos, por isso alguns inquilinos ainda estão usando um design mais antigo. Até ao final de maio de 2020, os convites de todos os inquilinos estarão a usar este modelo.

## <a name="explaining-the-email"></a>Explicando o e-mail

Vamos ver alguns elementos do e-mail para que saiba a melhor forma de usar as suas capacidades.

### <a name="subject"></a>Assunto

O assunto do e-mail segue este padrão:

&lt;nome de utilizador &gt; convidou-o a aceder a aplicações dentro da sua organização.

### <a name="from-address"></a>Do endereço

Usamos um padrão semelhante ao LinkedIn para o endereço From. Este padrão deve deixar claro que, embora o e-mail venha invites@microsoft.com de, o convite é de outra organização. O formato é: Microsoft Invitations  <invites@microsoft.com> ou Microsoft invitations em nome do nome de &lt; &gt;  <invites@microsoft.com> inquilino. 

### <a name="reply-to"></a>Resposta a

O e-mail de resposta é definido para o e-mail do convidado quando disponível, de modo que responder ao e-mail envia um e-mail de volta para o convidado.

### <a name="phishing-warning"></a>Aviso de phishing

O e-mail começa com um breve aviso ao utilizador sobre phishing, alertando-os de que só devem aceitar convites que esperam. É uma boa prática garantir que os parceiros que convida não se surpreendam com o seu convite mencionando-lhes antecipadamente.

![Imagem do aviso de phishing no e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informação do convidado

O e-mail inclui informações sobre o convidado e a organização de onde estão a enviar o convite. Isto inclui o nome e endereço de e-mail do remetente, bem como o nome e o domínio primário associados à organização. Todas estas informações devem ajudar o convidado a tomar uma decisão informada sobre a aceitação do convite.

![Imagem da informação do convidado no e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Mensagem de convite

Se o convidado incluir uma mensagem como parte do seu convite quando [convida um utilizador convidado para o diretório, grupo ou app](add-users-administrator.md) ou quando utiliza o convite [API,](customize-invitation-api.md)a mensagem é destacada na secção principal do e-mail. Também estão incluídos o nome e a imagem do perfil do convidado se definirem um. A mensagem em si é uma área de texto, por isso, por razões de segurança, não processa tags HTML.

![Imagem da mensagem de convite no e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Aceite o botão e redirecione o URL

A próxima secção do e-mail contém informações sobre onde o convite será levado depois de aceitarem o convite, bem como um botão para o fazer.  No futuro, o convidado pode sempre utilizar este link para retornar diretamente aos seus recursos.

![Imagem do botão aceitar e redirecionar URL no e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Seção footer

O rodapé contém mais informações sobre o convite que está a ser enviado. Há sempre uma opção para o convidado bloquear futuros convites. Se a organização [tiver estabelecido uma declaração de privacidade,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)o link para a declaração é apresentado aqui.  Caso contrário, uma nota indica que a organização não definiu uma declaração de privacidade.

![Imagem da secção de rodapé no e-mail](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Bloquear uma organização (não subscrever)

No convite de uma organização, o rodapé contém uma opção para **bloquear futuros convites.** Um utilizador convidado pode selecionar este link para bloquear quaisquer convites futuros da organização. Esta ação também adiciona a organização à lista não inscrita do utilizador em [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Ver organizações que bloqueou

Um utilizador convidado pode seguir estes passos para ver ou exportar as organizações que bloqueou:

1. Vai [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) para.
2. Insira o seu e-mail e siga os passos de inscrição para a autenticação de senha de e-mail.
3. Veja as organizações que bloqueou ou exporte os nomes usando cópia e pasta.
   > [!NOTE]
   > Se quiser permitir que uma organização que bloqueou o convide novamente, pode escolher a organização e selecionar **Next**.

## <a name="how-the-language-is-determined"></a>Como a língua é determinada

O idioma apresentado ao utilizador convidado no e-mail do convite é determinado pelas seguintes definições. Estas definições estão listadas por ordem de precedência. Se uma definição não estiver configurada, a próxima definição da lista determina o idioma.

- A **mensagem Propriedade deLanguage** do objeto [convidadoUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se o convite Create API for usado
-   A propriedade **preferida da Língua de Língua** especificada no objeto de [utilizador](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) do hóspede
-   A **linguagem de notificação** definida nas propriedades do inquilino do hóspede (apenas para inquilinos da AZure AD)
-   A **linguagem de notificação** definida nas propriedades do inquilino de recursos

Se nenhuma destas definições estiver configurada, o idioma não é lívado para inglês (EUA).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração Azure AD B2B](what-is-b2b.md)
- [Como é que os administradores do Azure Ative Directory adicionam utilizadores de colaboração B2B?](add-users-administrator.md)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicione utilizadores de colaboração B2B sem convite](add-user-without-invite.md)
