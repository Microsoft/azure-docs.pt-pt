---
title: Obtenha ajuda para aceder e utilizar o portal MyApps - Azure Active Directory | Documentos da Microsoft
description: Obtenha ajuda com a iniciar sessão e a execução de tarefas comuns no painel de acesso.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad68afd02694ec471d032a554ed468691ff914b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105476"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-myapps-portal"></a>Resolver problemas ao aceder e utilizar o portal MyApps

Se estiver a ter problemas com a iniciar sessão ou com o portal MyApps, experimente estas sugestões de resolução de problemas, antes de contactar o suporte técnico ou o seu administrador para obter ajuda.

## <a name="i-am-having-trouble-signing-into-the-myapps-portal"></a>Estou a ter problemas ao iniciar sessão no portal do My Apps

Experimente estas sugestões gerais:

- Primeiro, a verificação para ver se estão a utilizar o URL correto, [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Tente adicionar o URL para sites fidedignos do seu browser.
- Certifique-se de que a palavra-passe está correta e não expirou. Para obter mais informações, consulte [repor a palavra-passe profissional ou escolar](active-directory-passwords-update-your-own-password.md).
- Verifique para garantir que as informações de contacto de autenticação estão atualizados e não a bloquear o acesso do utilizador. Para obter mais informações, consulte [o que o Azure multi-factor Authentication significa para mim?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Experimente limpar os cookies do browser e volte a tentar iniciar sessão novamente.

Se ainda tiver com problemas ao tentar iniciar sessão, contacte o administrador.

## <a name="i-seem-to-be-having-password-issues"></a>Parece estar a ter problemas de palavra-passe

Se esqueceu-se a sua palavra-passe, nunca recebeu um da sua equipa de TI, é bloqueado a sua conta ou para alterar a palavra-passe, veja [obter ajuda, esqueci-me minha palavra-passe do Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Eu preciso me registrar para a reposição de palavra-passe

Pode repor a palavra-passe ou desbloquear a sua conta sem ter de falar com alguém, a através da reposição de palavra-passe self-service (SSPR). Antes de poder utilizar esta funcionalidade, tem de registar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que requer que o administrador. Para obter mais informações, consulte [Registre-se para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Estou a ter problemas a instalar o início de sessão extensão My segura de aplicações em

O portal MyApps requer um browser que suporte a JavaScript e CSS ativou a. Se estiver a utilizar com base em palavra-passe único início de sessão em aplicações, a extensão que acompanha este artigo deve ser instalada também. Esta extensão é transferida automaticamente quando inicia um aplicativo que está configurado para a palavra-passe únicas início de sessão em aplicações.

Verifique para se certificar de que está a cumprir os seguintes requisitos de browser:

- **Microsoft Edge**: Windows 10 Anniversary Edition ou posterior.
- **Chrome**: no Windows 7 ou posterior e no Mac OS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.
- **Internet Explorer 11**: no Windows 7 ou posterior (suporte limitado).

Também pode transferir a extensão diretamente a partir os seguintes sites:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Se tiver instalado a extensão e ainda está a ter problemas, experimente o seguinte:

- Verifique as definições de extensão de browser para garantir que a extensão está ativada.
- Reinicie o navegador e iniciar sessão MyApps portal.
- Limpar os cookies do browser e inicie sessão no MyApps portal.
- Para ter acesso a uma ferramenta de diagnóstico e instruções passo a passo sobre como configurar a extensão para o Internet Explorer, consulte [resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Utilize a extensão de início de sessão de seguro das minhas aplicações
* Se estiver a utilizar um URL de aplicações My diferente de `https://myapps.microsoft.com`, configurar o URL predefinido ao fazer o seguinte:
   1. Enquanto estiver *não* entrar para a extensão, com o botão direito no ícone de extensão.
   2. No menu, selecione **URL de aplicações My**.
   3. Selecione o URL predefinido.
   4. Selecione o ícone de extensão.
   5. Para iniciar sessão para a extensão, selecione **iniciar sessão começar a utilizar**.

* Para iniciar sessão diretamente para uma aplicação a partir do browser, faça o seguinte:
   1. Depois de instalar a extensão, iniciar sessão na mesma selecionando **iniciar sessão começar a utilizar**.
   2. Inicie sessão na aplicação com o URL de início de sessão.  
       O URL de início de sessão é, normalmente, o URL da aplicação que exibe o formulário de início de sessão.
      A extensão deve alterar o estado e permitem-lhe saber que uma palavra-passe está disponível.
   3. Para iniciar sessão, selecione o ícone de extensão.

* Para iniciar uma aplicação a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, iniciar sessão na mesma selecionando **iniciar sessão começar a utilizar**.
   2. Selecione o ícone de extensão para abrir respetivo menu.
   3. Procurar uma aplicação que está disponível no MyApps portal.
   4. Na lista de resultados da pesquisa, selecione a aplicação.  
       As últimas três aplicações que já utilizou são apresentadas no **utilizados recentemente** lista de atalho.

> [!NOTE]
> Estas opções estão disponíveis apenas para o Microsoft Edge, Chrome e Firefox.

## <a name="how-do-i-add-a-new-app"></a>Como posso adicionar uma nova aplicação?

1. Sobre o **aplicações** página, selecione **Adicionar aplicação**.
2. Procure a aplicação que pretende adicionar e, em seguida, selecione **adicionar**.

   > [!NOTE]
   > * Pode aceder a esta opção apenas se o administrador tiver ativado para a sua conta.
   > * Se a aplicação requer permissão, poderá ter de aguardar a aprovação do administrador.

## <a name="how-do-i-manage-my-group-memberships"></a>Como posso gerir as minhas associações de grupo?

Selecione o **grupos** mosaico e, em seguida, efetue o seguinte:
* Para criar um grupo, em **grupos que possuo**, selecione **criar grupo**e, em seguida, siga as instruções.
* Para aderir a um grupo, em **grupos a que pertenço**, selecione **aderir a grupo**e, em seguida, siga as instruções.

   > [!NOTE]
   > * Pode aceder a esta opção apenas se o administrador tiver ativado para a sua conta.
   > * Se for um membro de um grupo, pode ver os detalhes e deixar o grupo.
   > * Se for um proprietário de um grupo, pode ver os detalhes, adicionar ou remover membros e deixar o grupo.
