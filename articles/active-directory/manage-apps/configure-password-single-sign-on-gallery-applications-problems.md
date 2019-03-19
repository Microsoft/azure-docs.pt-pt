---
title: Problema ao configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Compreender os rostos de pessoas de problemas comuns durante a configuração do logon único de palavra-passe para aplicações que já estão listados na Galeria de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6c313cc654461ea22c126fede317ed230408b82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079201"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema ao configurar a palavra-passe início de sessão único para uma aplicação da galeria do Azure AD

Este artigo ajuda-o a compreender os rostos de pessoas de problemas comuns durante a configuração do **o início de sessão único de palavra-passe** com um aplicativo de galeria do Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não submetê-las

Este problema ocorre, normalmente, se o fornecedor do aplicativo tiver alterado sua página de início de sessão recentemente para adicionar um campo, alterar um identificador usado para detectar os campos de nome de utilizador e palavra-passe ou modificado como o início de sessão experiência funciona para seus aplicativos. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente estes problemas.

Embora a Microsoft tem as tecnologias para detetar automaticamente quando integrações quebrar, pode não ser possível localizar os problemas imediatamente ou os problemas demoram algum tempo a corrigir. No caso de quando uma dessas integrações não funciona corretamente, abra um incidente de suporte para que pode ser corrigido mais rapidamente possível.

**Se for contato com o fornecedor desse aplicativo,** enviá-los nossa maneira para que a Microsoft pode trabalhar com eles para integrar nativamente seus aplicativos com o Azure Active Directory. Pode enviar o fornecedor para o [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](../develop/howto-app-gallery-listing.md) para obtê-las iniciado.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais são preenchidas bem como submetidas, mas a página indica que as credenciais estão incorretas

Para resolver este problema, tente primeiro essas coisas:

- Peça ao utilizador que primeiro tente **entrar diretamente para o site de aplicativos** com as credenciais armazenadas para eles.

  * Se o início de sessão funciona, em seguida, tem do usuário clique no **atualizar as credenciais** botão a **mosaico de aplicação** no **aplicações** secção do [acesso à aplicação Painel](https://myapps.microsoft.com/) atualizá-los para o trabalho nome de utilizador e palavra-passe mais recente conhecidos.

  * Se ou outro administrador atribuir as credenciais para este utilizador, encontrar o utilizador ou a atribuição do grupo da aplicação ao navegar para o **utilizadores e grupos** separador da aplicação, selecionando a atribuição e clicar o  **Atualizar credenciais** botão.

- Se o utilizador atribuído das suas próprias credenciais, peça ao utilizador **Certifique-se não se esqueça de que a palavra-passe não expirou no aplicativo** e, se assim for, **atualizar a palavra-passe expirada** ao iniciar sessão na aplicação diretamente.

  * Depois da palavra-passe foi atualizada no aplicativo, solicitar o usuário clique a **atualizar as credenciais** botão a **mosaico de aplicação** no **aplicações** secção do [Painel de acesso de aplicação](https://myapps.microsoft.com/) atualizá-los para o trabalho nome de utilizador e palavra-passe mais recente conhecidos.

  * Se ou outro administrador atribuir as credenciais para este utilizador, encontrar o utilizador ou a atribuição do grupo da aplicação ao navegar para o **utilizadores e grupos** separador da aplicação, selecionando a atribuição e clicar o  **Atualizar credenciais** botão.

- O utilizador atualizar a extensão de browser do painel de acesso ao seguir os passos abaixo a [como instalar a extensão de Browser do painel de acesso](#how-to-install-the-access-panel-browser-extension) secção.

- Certifique-se de que a extensão de browser do painel de acesso está em execução e ativado no browser do utilizador.

- Certifique-se de que os utilizadores não estão a tentar iniciar sessão para a aplicação do painel de acesso ao mesmo tempo no **modo inPrivate, incógnito ou privado**. A extensão do painel de acesso não é suportada nesses modos.

No caso de sugestões anteriores funcionar, pode ser o caso em que uma alteração ocorreu no lado do aplicativo que está temporariamente quebrado a integração do aplicativo com o Azure AD. Por exemplo, isto pode ocorrer quando o fornecedor do aplicativo introduz um script na respetiva página de que tem um comportamento diferente para o vs manuais automatizada de entrada, que faz com que automatizadas de integração, como o nosso próprio, interromper. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente estes problemas.

Embora a Microsoft tem as tecnologias para detetar automaticamente quando interromper integrações de aplicação, pode não ser possível localizar os problemas imediatamente ou os problemas poderão demorar algum tempo para corrigir. Quando uma integração não funciona corretamente, pode abrir um incidente de suporte para corrigir o mais rapidamente possível. 

Além disso, **se for contato com o fornecedor desse aplicativo,** **enviá-los nossa maneira** para que possa trabalhar com eles para integrar nativamente seus aplicativos com o Azure Active Directory. Pode enviar o fornecedor para o [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](../develop/howto-app-gallery-listing.md) para obtê-las iniciado.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e Firefox, mas não no Internet Explorer

Existem duas das principais causas para este problema:

- Dependendo das configurações de segurança ativadas no Internet Explorer, se o Web site não é parte de um **zona fidedignos**, às vezes, nosso script impedidos de execução para a aplicação.

  *  Para resolver este problema, indique o usuário **adicionar o site da aplicação** para o **Sites confiáveis** lista dentro de suas **definições de segurança do Internet Explorer**. Pode enviar os seus utilizadores para o [como adicionar um site à minha lista de sites fidedignos](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artigo para obter instruções detalhadas.

- Em raras circunstâncias, validação de segurança do Internet Explorer, às vezes, pode fazer com que a página carregar mais lenta do que a execução do nosso script.

  * Infelizmente, esta situação pode variar consoante a versão do navegador, a velocidade do computador ou a sites visitados. Neste caso, sugerimos que contacte o suporte para que possamos corrigir a integração para esta aplicação específica.

Além disso, **se for contato com o fornecedor desse aplicativo,** **enviá-los nossa maneira** para que possa trabalhar com eles para integrar nativamente seus aplicativos com o Azure Active Directory. Pode enviar o fornecedor para o [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](../develop/howto-app-gallery-listing.md) para obtê-las iniciado.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de início de sessão da aplicação foi alterada recentemente ou requer um campo adicional

Se a página de início de sessão do aplicativo mudou drasticamente, às vezes, isso faz com que nosso integrações interromper. Um exemplo disso é quando um fornecedor de aplicativos adiciona um campo de início de sessão, um captcha ou a autenticação multifator para suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente estes problemas.

Embora a Microsoft tem as tecnologias para detetar automaticamente quando interromper integrações de aplicação, pode não ser possível localizar os problemas imediatamente ou os problemas poderão demorar algum tempo para corrigir. Quando uma integração não funciona corretamente, pode abrir um incidente de suporte para corrigir o mais rapidamente possível. 

Além disso, **se for contato com o fornecedor desse aplicativo,** **enviá-los nossa maneira** para que possa trabalhar com eles para integrar nativamente seus aplicativos com o Azure Active Directory. Pode enviar o fornecedor para o [listar a sua aplicação na Galeria de aplicações do Azure Active Directory](../develop/howto-app-gallery-listing.md) para obtê-las iniciado.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos browsers suportados e inicie sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação SSO de palavra-passe** no painel de acesso.

3.  Na linha de comandos pedindo para instalar o software, selecione **instalar agora**.

4.  Com base no seu navegador, será direcionado para a ligação de transferência. **Adicionar** a extensão ao seu navegador.

5.  Se o browser pede-lhe, selecione a qualquer um **habilitar** ou **permitir** a extensão.

6.  Depois de instalada **reiniciar** a sessão do browser.

7.  Iniciar sessão no painel de acesso e veja se pode **inicie** seus aplicativos de SSO de palavra-passe

Também pode baixar a extensão para o Chrome e Firefox dos links diretos abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

