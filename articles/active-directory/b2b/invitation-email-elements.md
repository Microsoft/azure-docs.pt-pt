---
title: Elementos da mensagem de e-mail de convite B2B - Azure Active Directory | Documentos da Microsoft
description: Modelo do e-mail de convite de colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fa8148fff1389982d967b2e69f4a9425841c91
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014911"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos da mensagem de e-mail de convite de colaboração B2B - Azure Active Directory

E-mails de convite são uma componente fundamental para colocar os parceiros na organização como utilizadores de colaboração B2B no Azure AD. Pode usá-los para aumentar a confiança do destinatário. Pode adicionar a legitimidade e prova de redes sociais para o e-mail, para se certificar de que o destinatário sente confortável selecionando o **começar** botão para aceitar o convite. Esta demonstração de confiança é uma chave significa que reduzir o atrito partilha. E também queira fazer o e-mail ótimo!

![E-mail de convite do Azure AD B2b](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explicar a mensagem de e-mail
Vamos examinar alguns elementos da mensagem de e-mail para que saiba a melhor maneira de usar seus recursos.

### <a name="subject"></a>Requerente
O assunto do e-mail segue o padrão seguinte: Está convidado para o &lt;tenantname&gt; organização

### <a name="from-address"></a>Do endereço
Utilizamos um padrão como o LinkedIn para o endereço.  Deve ser clara de quem é o autor do convite e de que o da empresa e também esclarecer que o e-mail é proveniente de um Microsoft endereço de e-mail. O formato é: Microsoft Invitations <invites@microsoft.com> ou &lt;nome a apresentar do autor do convite&gt; partir &lt;tenantname&gt; (através da Microsoft) <invites@microsoft.com>.

### <a name="reply-to"></a>Responder A
O e-mail de resposta está definido para o e-mail na máquina, quando disponível, para que a responder à mensagem de e-mail envia um e-mail para o autor do convite.

### <a name="branding"></a>Personalização
Os e-mails de convite da sua utilização do inquilino a imagem corporativa que pode ter configurado para o seu inquilino. Se quiser tirar partido desta capacidade, [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) são os detalhes sobre como configurá-lo. O logótipo de faixa é apresentado no e-mail. Siga o tamanho da imagem e instruções de qualidade [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) para obter melhores resultados. Além disso, o nome da empresa também aparece na chamada para a ação.

### <a name="call-to-action"></a>Plano de ação
O apelo à ação consiste em duas partes: explicando por que o destinatário recebeu o e-mail e o que o destinatário está a ser-lhe pedido para fazer a respeito.
- A secção "Porquê" pode ser resolvida usando o seguinte padrão: Foi convidado para aceder às aplicações na &lt;tenantname&gt; organização

- E o "o que estiver sendo solicitado a fazer" secção é indicada pela presença do **começar** botão. Quando o destinatário for adicionado sem a necessidade de convites, este botão não é exibida.

### <a name="inviters-information"></a>Informações do autor do convite
Nome a apresentar na máquina é incluído no e-mail. E, além disso, se tiver configurado a uma imagem de perfil para a sua conta do Azure AD, o e-mail de convite irá incluir essa imagem também. Ambos têm a finalidade de aumentar a confiança de seu destinatário do e-mail.

Se ainda não configurou a sua imagem do perfil, é apresentado um ícone com iniciais na máquina no lugar da imagem:

  ![Exibindo iniciais na máquina](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Corpo
O corpo contém a mensagem que o autor do convite compõe quando [convidar um utilizador convidado ao diretório, grupo ou aplicação](add-users-administrator.md) ou [ao utilizar o API de convite](customize-invitation-api.md). É uma área de texto, para que ele não processa tags de HTML por motivos de segurança.

  ![imagem do corpo do e-mail](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Secção de rodapé
O rodapé contém a marca da empresa do Microsoft e permite ao destinatário saber se o e-mail foi enviado a partir de um alias não monitorizado. 

Casos especiais:

- O autor do convite não tiver um endereço de e-mail no inquilino de convite

  ![imagem do autor do convite não tiver um endereço de e-mail no inquilino de convite](media/invitation-email-elements/inviter-no-email.png)


- O destinatário não precisa resgatar o convite

  ![Quando o destinatário não precisa resgatar o convite](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Como o idioma é determinado
A linguagem apresentada ao utilizador convidado no e-mail de convite é determinada pelas seguintes definições. Estas definições estão listadas na ordem de precedência. Se uma definição não estiver configurada, a definição seguinte na lista determina o idioma. 
- O **messageLanguage** propriedade da [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se o convite de criar API é utilizado de objeto
-   O **preferredLanguage** propriedade especificada do convidado [objeto de utilizador](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   O **idioma de notificação** definido nas propriedades de principal de inquilino do utilizador convidado (para inquilinos do Azure AD)
-   O **idioma de notificação** definido nas propriedades do inquilino de recursos

Se nenhuma destas definições está configurada, o idioma padrão é para inglês (EUA).

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD](what-is-b2b.md)
- [Como é que os administradores do Azure Active Directory adicionar utilizadores de colaboração B2B?](add-users-administrator.md)
- [Como é que os operadores de informações adicionar utilizadores de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar utilizadores de colaboração do B2B sem convite](add-user-without-invite.md)
