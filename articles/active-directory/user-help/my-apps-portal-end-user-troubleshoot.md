---
title: Obtenha ajuda com o portal My Apps - Azure Ative Directory| Microsoft Docs
description: Obtenha ajuda para iniciar sessão e executar tarefas comuns no portal My Apps.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 4377ed76de971f78336ea9024b59dafc5d513487
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100094972"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Problemas de resolução de problemas com o portal My Apps

Se estiver a ter problemas em iniciar sessão ou utilizar o portal **My Apps,** experimente estas dicas de resolução de problemas antes de contactar o Helpdesk ou o seu administrador para obter ajuda.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Estou com dificuldades em instalar a extensão de inscrição segura das minhas apps

Se tiver problemas em instalar a extensão de inscrição segura das minhas apps:

- Certifique-se de que está a usar um browser suportado, incluindo:

    - **Microsoft Edge.** A correr na Edição de Aniversário do Windows 10 ou mais tarde.

    - **Google Chrome.** A correr no Windows 7 ou mais tarde, e no macOS X ou mais tarde.

    - **Mozilla Firefox 26.0 ou mais tarde.** Em execução no Windows XP SP2 ou posteriormente, e no macOS X 10.6 ou mais tarde.

    - **Internet Explorer 11.** A correr no Windows 7 ou mais tarde (suporte limitado).

- Certifique-se de que as definições de extensão do seu navegador estão ligadas.

- Tente reiniciar o seu navegador e iniciar sessão no portal **My Apps** novamente.

- Tente limpar os cookies do seu navegador e, em seguida, reinicie e inscreva-se novamente no portal **My Apps.**

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Não posso entrar no portal **My Apps**

Se tiver problemas em iniciar sessão no portal **My Apps,** pode experimentar o seguinte:

- Se vir um erro de sessão com uma conta pessoal da Microsoft, ainda pode iniciar sessão utilizando o nome de domínio da sua organização (como contoso.com) ou o **ID** do Inquilino da sua organização a partir de um dos seguintes URLs:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

- Certifique-se de que está a usar a URL certa. Deve ser https://myapps.microsoft.com ou uma página personalizada para a sua organização, https://myapps.microsoft.com/contoso.com como.

- Certifique-se de que a sua palavra-passe está correta e que ainda não expirou. Para obter mais informações, consulte [Redefinir o seu trabalho ou a palavra-passe da escola.](active-directory-passwords-update-your-own-password.md)

- Certifique-se de que a sua informação de verificação é atual e precisa. Para obter mais informações, veja [o que significa para mim a autenticação multi-factor Azure AD ou](./multi-factor-authentication-end-user-first-time.md) [alterando os seus métodos e informações de informação de segurança](./security-info-setup-auth-app.md).

- Adicione o URL do portal **My App** à configuração de sites de segurança > segurança > > **de segurança.**

- Limpe a cache do seu navegador e tente entrar novamente.

## <a name="my-password-isnt-working"></a>A minha senha não está a funcionar.

Se esqueceu a sua palavra-passe, nunca recebeu uma da sua organização, está trancada fora da sua conta, ou quer alterar a sua palavra-passe, consulte [Ajuda, esqueci-me da minha senha de AD Azure.](active-directory-passwords-update-your-own-password.md)

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Quero ser capaz de redefinir a minha própria senha.

Para poder redefinir a sua própria palavra-passe, o seu administrador tem primeiro de ligar a funcionalidade para a sua organização e, em seguida, tem de atualizar e verificar os métodos de verificação necessários. Para obter mais informações sobre como atualizar os seus métodos de verificação, consulte [Registar para reposição da palavra-passe de autosserviço](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Estou a receber uma mensagem de Acesso Negada quando começo uma aplicação

Se receber uma mensagem **Access Negada** depois de iniciar uma aplicação a partir do portal **My App,** pode experimentar o seguinte:

- Certifique-se de que instalou a [extensão de inscrição segura das minhas aplicações](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) e que está a utilizar um [browser suportado.](my-apps-portal-end-user-access.md#supported-browsers)

- Certifique-se de que está a usar o URL certo para a aplicação e que o URL está na sua lista de sites de > de Segurança > De Segurança da **Internet.**

- Certifique-se de que a sua palavra-passe está correta e que ainda não expirou. Para obter mais informações, consulte [Redefinir o seu trabalho ou a palavra-passe da escola.](active-directory-passwords-update-your-own-password.md)

- Certifique-se de que a sua informação de verificação é atual e precisa. Para obter mais informações, veja [o que significa para mim a autenticação multi-factor Azure AD ou](./multi-factor-authentication-end-user-first-time.md) [alterando os seus métodos e informações de informação de segurança](./security-info-setup-auth-app.md).

- Limpe a cache do seu navegador e tente entrar novamente.

Se depois de experimentar estas coisas ainda não conseguir aceder à sua aplicação, deve contactar o balcão de ajuda da sua organização para obter assistência.

## <a name="next-steps"></a>Passos seguintes

Depois de iniciar sessão no portal **My Apps,** também pode atualizar as informações de perfil e conta, as informações do seu grupo e aceder a informações de revisão (se tiver permissão).

- [Aceda e utilize aplicativos no portal My Apps](my-apps-portal-end-user-access.md).

- [Altere as informações do seu perfil.](./my-account-portal-settings.md)

- [Ver e atualizar as informações relacionadas com os grupos.](my-apps-portal-end-user-groups.md)

- [Execute as suas próprias avaliações de acesso.](my-apps-portal-end-user-access-reviews.md)
