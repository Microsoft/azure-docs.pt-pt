---
title: Problemas que configuram a palavra-passe SSO para a aplicação AZure AD Gallery
description: Compreenda os problemas comuns que as pessoas enfrentam ao configurar o sign-on único da palavra-passe para aplicações que já estão listadas na Galeria de Aplicações AD Azure
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fda2657a68fc8a3fd293c0c6001a71f5970548
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763521"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema de configuração de senha único para uma aplicação da Galeria AD AZure

Este artigo ajuda-o a compreender os problemas comuns que as pessoas enfrentam ao configurar **o Sign-on Único da Palavra-Passe** com uma aplicação da Galeria AD Azure.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as submete

Este problema normalmente acontece se o fornecedor de aplicações mudou a sua página de entrada recentemente para adicionar um campo, alterou um identificador utilizado para detetar o nome de utilizador e os campos de palavra-passe, ou modificado como a experiência de entrada funciona para a sua aplicação. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Embora a Microsoft tenha tecnologias para detetar automaticamente quando as integrações quebram, pode não ser possível encontrar os problemas imediatamente, ou os problemas demoram algum tempo a corrigir. No caso de uma destas integrações não funcionar corretamente, abra um processo de apoio para que possa ser corrigido o mais rapidamente possível.

**Se estiver em contacto com o fornecedor desta aplicação,** envie-as à nossa maneira para que a Microsoft possa trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../azuread-dev/howto-app-gallery-listing.md) começar.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais são preenchidas e submetidas, mas a página indica que as credenciais estão incorretas

Para resolver esta questão, primeiro tente estas coisas:

- O utilizador primeiro tente **iniciar sustura no site da aplicação diretamente** com as credenciais armazenadas para os mesmos.

  * Se o pedido de sômis funcionar, o utilizador clique no botão **'Atualização'** de credenciais no Bloco de **Aplicações** na secção **aplicações** do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) para os atualizar para o mais recente nome de utilizador e palavra-passe de trabalho conhecido.

  * Se você, ou outro administrador atribuiu as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou grupo navegando no separador **Grupos & Utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de Atualização'.**

- Se o utilizador tiver atribuído as suas próprias credenciais, certifique-se de que a **sua palavra-passe não expirou na aplicação** e, em caso afirmativo, **atualize a sua palavra-passe expirada** ao iniciar sessão diretamente na aplicação.

  * Depois de a palavra-passe ter sido atualizada na aplicação, solicite ao utilizador que clique no botão **de credenciais De Atualização** no Bloco de **Aplicações** na secção **aplicações** do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) para os atualizar para o mais recente nome de utilizador e senha de utilizador de trabalho conhecido.

  * Se você, ou outro administrador atribuiu as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou grupo navegando no separador **Grupos & Utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de Atualização'.**

- Tenha o utilizador de atualizar a extensão do navegador do painel de acesso seguindo os passos abaixo na secção como instalar a secção [de extensão do Navegador do Painel de Acesso.](#how-to-install-the-access-panel-browser-extension)

- Certifique-se de que a extensão do navegador do painel de acesso está em execução e ativada no browser do seu utilizador.

- Certifique-se de que os seus utilizadores não estão a tentar iniciar seduções na aplicação a partir do painel de acesso enquanto estiver **incógnito, incógnito ou modo Privado**. A extensão do painel de acesso não é suportada nestes modos.

Caso as sugestões anteriores não funcionem, pode acontecer uma alteração no lado da aplicação que infringiu temporariamente a integração da aplicação com a Azure AD. Por exemplo, isto pode ocorrer quando o fornecedor de aplicações introduz um script na sua página que se comporta de forma diferente para entrada manual vs automatizada, o que faz com que a integração automatizada, como a nossa, se rompa. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Apesar de a Microsoft ter tecnologias para detetar automaticamente quando as integrações da aplicação quebram, pode não ser possível encontrar os problemas de imediato, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de apoio para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-os à nossa maneira** para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../azuread-dev/howto-app-gallery-listing.md) começar.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e Firefox, mas não no Internet Explorer

Há duas causas principais para esta questão:

- Dependendo das definições de segurança ativadas no Internet Explorer, se o website não fizer parte de uma **Zona Fidedigna,** por vezes o nosso script fica bloqueado de executar para a aplicação.

  *  Para resolver isto, instrua o utilizador a **adicionar o website da aplicação** à lista de Sites **Fidedignos** dentro das suas **definições de segurança do Internet Explorer**. Pode enviar os seus utilizadores para o [Como adicionar um site ao artigo da lista de sites fidedignos](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) para obter instruções detalhadas.

- Em circunstâncias raras, a validação de segurança do Internet Explorer pode, por vezes, fazer com que a página carregue mais lentamente do que a execução do nosso script.

  * Infelizmente, esta situação pode variar dependendo da versão do navegador, velocidade do computador ou site visitado. Neste caso, sugerimos que contacte o suporte para que possamos corrigir a integração para esta aplicação específica.

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-os à nossa maneira** para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../azuread-dev/howto-app-gallery-listing.md) começar.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de login da aplicação mudou recentemente ou se requer um campo adicional

Se a página de login da aplicação mudou drasticamente, por vezes isto faz com que as nossas integrações se rompam. Um exemplo disso é quando um fornecedor de aplicações adiciona um campo de inscrição, uma captcha ou autenticação multi-factor às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Apesar de a Microsoft ter tecnologias para detetar automaticamente quando as integrações da aplicação quebram, pode não ser possível encontrar os problemas de imediato, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de apoio para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-os à nossa maneira** para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../azuread-dev/howto-app-gallery-listing.md) começar.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu AD Azure.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na instrução pedindo para instalar o software, **selecione Instalar Agora**.

4.  Com base no seu navegador, você está direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **Ativar** ou **Permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sessão do seu navegador.

7.  Inicie seduca no Painel de Acesso e veja se consegue **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Próximos passos
[Fornecer um único s-on às suas apps com Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

