---
title: Elementos do e-mail de convite B2B - Diretório Ativo Azure / Microsoft Docs
description: Modelo de convite de convite de colaboração Azure Ative Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768359"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos do convite de colaboração B2B - Azure Ative Directory

Os e-mails de convite são uma componente crítica para trazer parceiros a bordo como utilizadores de colaboração B2B em Azure AD. Pode usá-los para aumentar a confiança do destinatário. pode adicionar legitimidade e prova social ao e-mail, para garantir que o destinatário se sinta confortável ao selecionar o botão **Get Started** para aceitar o convite. Esta confiança é um meio fundamental para reduzir a partilha de atritos. E também quer que o e-mail fique ótimo!

![Screenshot mostrando o e-mail de convite B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explicando o e-mail
Vamos ver alguns elementos do e-mail para que saiba a melhor forma de usar as suas capacidades.

### <a name="subject"></a>Assunto
O tema do e-mail segue o seguinte &lt;padrão:&gt; Você é convidado para a organização do nome do inquilino

### <a name="from-address"></a>A partir do endereço
Usamos um padrão linkedIn para o endereço From.  Deve ser claro quem é o convidado e de que empresa, e também esclarecer que o e-mail vem de um endereço de e-mail da Microsoft. O formato é: <invites@microsoft.com> &lt;Microsoft Invitations&gt; ou &lt;Display&gt; name of <invites@microsoft.com>inviter from tenantname (via Microsoft) .

### <a name="reply-to"></a>Resposta a
O e-mail de resposta ao e-mail é definido para o e-mail do convidado quando disponível, de modo que responder ao e-mail envia um e-mail de volta para o convidado.

### <a name="branding"></a>Imagem corporativa
Os e-mails de convite do seu inquilino usam a marca da empresa que você pode ter configurado para o seu inquilino. Se quiser aproveitar esta capacidade, [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) estão os detalhes sobre como configurá-la. O logotipo do banner aparece no e-mail. Siga aqui o tamanho da imagem e as [instruções](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) de qualidade para obter os melhores resultados. Além disso, o nome da empresa também aparece no apelo à ação.

### <a name="call-to-action"></a>Chamada à ação
A chamada à ação consiste em duas partes: explicar porque é que o destinatário recebeu o correio e o que o destinatário está a ser solicitado a fazer em relação a isso.
- A secção "porquê" pode ser abordada usando o seguinte padrão: Foi convidado &lt;a&gt; aceder a aplicações na organização de nome de inquilinos

- E a secção "o que te pedem para fazer" é indicada pela presença do botão **Get Started.** Quando o destinatário foi adicionado sem a necessidade de convites, este botão não aparece.

### <a name="inviters-information"></a>Informações do convidado
O nome de exibição do convidado está incluído no e-mail. Além disso, se tiver configurado uma imagem de perfil para a sua conta Azure AD, o e-mail convidativo incluirá essa imagem também. Ambos destinam-se a aumentar a confiança do seu destinatário no e-mail.

Se ainda não configurar a sua imagem de perfil, é mostrado um ícone com as iniciais do convidado no lugar da imagem:

  ![Screenshot mostrando o convite com as iniciais de convite exibidas](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Corpo
O corpo contém a mensagem que o convidado compõe ao [convidar um utilizador convidado para o diretório, grupo ou app](add-users-administrator.md) ou utilizando o convite [API](customize-invitation-api.md). É uma área de texto, por isso não processa etiquetas HTML por razões de segurança.

  ![Screenshot mostrando o corpo do e-mail de convite](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Secção footer
O rodapé contém a marca da empresa Microsoft e informa o destinatário se o e-mail foi enviado de um pseudónimo não monitorizado. 

Casos especiais:

- O convidado não tem um endereço de e-mail no arrendamento convidativo

  ![Screenshot quando um convidado não tem e-mail no arrendamento convidativo](media/invitation-email-elements/inviter-no-email.png)


- O destinatário não precisa de resgatar o convite.

  ![Screenshot quando o destinatário não precisa resgatar convite](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Como a linguagem é determinada
O idioma apresentado ao utilizador convidado no e-mail de convite é determinado pelas seguintes definições. Estas definições estão listadas por ordem de precedência. Se uma definição não estiver configurada, a próxima definição na lista determina o idioma. 
- A propriedade **de mensagemIdioma** do objeto [UserMessageInfo convidado](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se o Convite Criar API é usado
-   A propriedade **preferredLanguage** especificada no objeto de [utilizador](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) do hóspede
-   A **linguagem de notificação** definida nas propriedades do inquilino do hóspede (apenas para inquilinos da AD Azure)
-   A **linguagem de notificação** definida nas propriedades do inquilino de recursos

Se nenhuma destas definições estiver configurada, o idioma não se aplica ao inglês (EUA).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração Azure AD B2B](what-is-b2b.md)
- [Como é que os administradores do Azure Ative Directory adicionam utilizadores de colaboração B2B?](add-users-administrator.md)
- [Como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicione utilizadores de colaboração B2B sem convite](add-user-without-invite.md)
