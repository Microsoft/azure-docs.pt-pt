---
title: Problemas ao configurar o SSO de senha para o aplicativo da galeria do Azure AD
description: Entenda os problemas comuns que as pessoas enfrentam ao configurar o logon único com senha para aplicativos que já estão listados na Galeria de aplicativos do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96e729bee0ac9beac447893ed8dbf1bba633e412
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274184"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema ao configurar o logon único com senha para um aplicativo da galeria do Azure AD

Este artigo ajuda você a entender os problemas comuns que as pessoas enfrentam ao configurar o **logon único com senha** com um aplicativo da galeria do Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as envia

Esse problema normalmente acontece se o fornecedor do aplicativo alterou sua página de entrada recentemente para adicionar um campo, alterou um identificador usado para detectar os campos de nome de usuário e senha ou modificou como a experiência de entrada funciona para seu aplicativo. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver esses problemas rapidamente.

Embora a Microsoft tenha tecnologias para detectar automaticamente quando as integrações são interrompidas, talvez não seja possível encontrar os problemas imediatamente, ou os problemas demoram algum tempo para serem corrigidos. No caso de uma dessas integrações não funcionar corretamente, abra um caso de suporte para que ele possa ser corrigido o mais rápido possível.

**Se você estiver em contato com o fornecedor deste aplicativo,** envie-o de nossa maneira para que a Microsoft possa trabalhar com eles para integrar nativamente seus aplicativos com o Azure Active Directory. Você pode enviar o fornecedor para a [listagem de seu aplicativo na Galeria de aplicativos Azure Active Directory](../develop/howto-app-gallery-listing.md) para que eles sejam iniciados.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais são preenchidas e enviadas, mas a página indica que as credenciais estão incorretas

Para resolver esse problema, primeiro tente estas coisas:

- Faça com que o usuário primeiro tente **entrar no site do aplicativo diretamente** com as credenciais armazenadas para eles.

  * Se a entrada funcionar, faça com que o usuário clique no botão **Atualizar credenciais** no **bloco do aplicativo** na seção **aplicativos** do painel de [acesso do aplicativo](https://myapps.microsoft.com/) para atualizá-los para o nome de usuário e a senha mais recentes conhecidos.

  * Se você ou outro administrador tiver atribuído as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou do grupo navegando até a guia **usuários & grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar credenciais** .

- Se o usuário tiver atribuído suas próprias credenciais, faça com que o usuário Verifique se a **senha não expirou no aplicativo** e, se estiver, **Atualize sua senha expirada** entrando diretamente no aplicativo.

  * Depois que a senha tiver sido atualizada no aplicativo, solicite que o usuário clique no botão **Atualizar credenciais** no **bloco do aplicativo** na seção **aplicativos** do painel de [acesso do aplicativo](https://myapps.microsoft.com/) para atualizá-los para o nome de usuário e a senha mais recentes conhecidos.

  * Se você ou outro administrador tiver atribuído as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou do grupo navegando até a guia **usuários & grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar credenciais** .

- Faça com que o usuário atualize a extensão do navegador do painel de acesso seguindo as etapas abaixo na seção [como instalar a extensão do navegador do painel de acesso](#how-to-install-the-access-panel-browser-extension) .

- Verifique se a extensão do navegador do painel de acesso está em execução e habilitada no navegador do usuário.

- Verifique se os usuários não estão tentando entrar no aplicativo no painel de acesso enquanto estiver no **modo Incognito, InPrivate ou Private**. Não há suporte para a extensão do painel de acesso nesses modos.

Caso as sugestões anteriores não funcionem, pode ser o caso em que uma alteração ocorreu no lado do aplicativo que interrompeu temporariamente a integração do aplicativo com o Azure AD. Por exemplo, isso pode ocorrer quando o fornecedor do aplicativo apresenta um script em sua página que se comporta de forma diferente para a entrada manual vs automatizada, o que faz com que a integração automatizada, como a nossa, seja quebrada. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver esses problemas rapidamente.

Embora a Microsoft tenha tecnologias para detectar automaticamente quando as integrações de aplicativos são interrompidas, talvez não seja possível encontrar os problemas imediatamente, ou os problemas podem levar algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para que ele seja corrigido o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor deste aplicativo,** **envie-os nossa maneira** para que possamos trabalhar com eles para integrar nativamente seu aplicativo com o Azure Active Directory. Você pode enviar o fornecedor para a [listagem de seu aplicativo na Galeria de aplicativos Azure Active Directory](../develop/howto-app-gallery-listing.md) para que eles sejam iniciados.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e no Firefox, mas não no Internet Explorer

Há duas causas principais para esse problema:

- Dependendo das configurações de segurança habilitadas no Internet Explorer, se o site não fizer parte de uma **zona confiável**, algumas vezes o script será impedido de ser executado para o aplicativo.

  *  Para resolver isso, instrua o usuário a **Adicionar o site do aplicativo** à lista de **sites confiáveis** em suas **configurações de segurança do Internet Explorer**. Você pode enviar seus usuários ao artigo [como adicionar um site à lista de sites confiáveis](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) para obter instruções detalhadas.

- Em raras circunstâncias, a validação de segurança do Internet Explorer pode, às vezes, fazer com que a página seja carregada mais lentamente do que a execução do nosso script.

  * Infelizmente, essa situação pode variar dependendo da versão do navegador, da velocidade do computador ou do site visitado. Nesse caso, sugerimos que você entre em contato com o suporte para que possamos corrigir a integração para esse aplicativo específico.

Além disso, **se você estiver em contato com o fornecedor deste aplicativo,** **envie-os nossa maneira** para que possamos trabalhar com eles para integrar nativamente seu aplicativo com o Azure Active Directory. Você pode enviar o fornecedor para a [listagem de seu aplicativo na Galeria de aplicativos Azure Active Directory](../develop/howto-app-gallery-listing.md) para que eles sejam iniciados.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verificar se a página de logon do aplicativo foi alterada recentemente ou requer um campo adicional

Se a página de logon do aplicativo tiver mudado drasticamente, às vezes isso faz com que nossas integrações sejam interrompidas. Um exemplo disso é quando um fornecedor de aplicativos adiciona um campo de entrada, um captcha ou autenticação multifator às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver esses problemas rapidamente.

Embora a Microsoft tenha tecnologias para detectar automaticamente quando as integrações de aplicativos são interrompidas, talvez não seja possível encontrar os problemas imediatamente, ou os problemas podem levar algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para que ele seja corrigido o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor deste aplicativo,** **envie-os nossa maneira** para que possamos trabalhar com eles para integrar nativamente seu aplicativo com o Azure Active Directory. Você pode enviar o fornecedor para a [listagem de seu aplicativo na Galeria de aplicativos Azure Active Directory](../develop/howto-app-gallery-listing.md) para que eles sejam iniciados.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, siga as etapas abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos navegadores com suporte e entre como um **usuário** no Azure AD.

2.  Clique em um **aplicativo de SSO de senha** no painel de acesso.

3.  No prompt solicitando a instalação do software, selecione **instalar agora**.

4.  Com base em seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se seu navegador perguntar, selecione para **habilitar** ou **permitir** a extensão.

6.  Depois de instalado, **reinicie** a sessão do navegador.

7.  Entre no painel de acesso e veja se você pode **Iniciar** seus aplicativos de SSO de senha

Você também pode baixar a extensão para o Chrome e Firefox nos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

