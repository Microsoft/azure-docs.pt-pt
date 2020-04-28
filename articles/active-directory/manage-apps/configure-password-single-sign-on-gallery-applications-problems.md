---
title: Problemas configurando senha SSO para app Azure AD Gallery
description: Compreenda os problemas comuns que as pessoas enfrentam ao configurar o Acesso Único de Senha para aplicações que já estão listadas na Galeria de Aplicações da AD Azure
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
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77159051"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema configurando o único sinal de senha para uma aplicação da Galeria AD Azure

Este artigo ajuda-o a compreender os problemas comuns que as pessoas enfrentam ao configurar o **Password Single Sign-on** com uma aplicação da Galeria AD Azure.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as submete

Este problema normalmente acontece se o fornecedor de aplicações tiver mudado a sua página de entrada recentemente para adicionar um campo, alterou um identificador usado para detetar o nome de utilizador e os campos de palavra-passe, ou modificou o funcionamento da experiência de entrada para a sua aplicação. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicações para resolver rapidamente estes problemas.

Embora a Microsoft tenha tecnologias para detetar automaticamente quando as integrações se rompem, pode não ser possível encontrar os problemas imediatamente, ou os problemas demoram algum tempo a corrigir. No caso de uma dessas integrações não funcionar corretamente, abra um caso de apoio para que possa ser corrigido o mais rapidamente possível.

**Se estiver em contacto com o fornecedor desta aplicação,** envie-lhes o nosso caminho para que a Microsoft possa trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o vendedor para a Listagem da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](../azuread-dev/howto-app-gallery-listing.md) para começar.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais são preenchidas e submetidas, mas a página indica que as credenciais estão incorretas

Para resolver esta questão, tente primeiro estas coisas:

- O utilizador tem primeiro a oportunidade de **iniciar sessão no site da aplicação diretamente** com as credenciais armazenadas para os mesmos.

  * Se o início de sessão funcionar, mande o utilizador clicar no botão **de credenciais De atualização** na secção **Aplicações** do Painel de Acesso a **Aplicações** para os atualizar ao mais recente nome de utilizador e palavra-passe conhecidos. [Application Access Panel](https://myapps.microsoft.com/)

  * Se você, ou outro administrador atribuir as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou do grupo navegando para o separador **Grupos & utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de actualização'.**

- Se o utilizador atribuiu as suas próprias credenciais, faça com que o utilizador verifique se a **sua palavra-passe não expirou na aplicação** e, em caso afirmativo, atualize a sua senha expirada, inserindo diretamente a sua **senha caducada.**

  * Depois de a palavra-passe ter sido atualizada na aplicação, solicite ao utilizador que clique no botão **de credenciais de Atualização** na secção **Aplicação Tile** na secção **Apps** access [Panel](https://myapps.microsoft.com/) para atualizá-las com o mais recente nome de utilizador e senha de trabalho conhecidos.

  * Se você, ou outro administrador atribuir as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou do grupo navegando para o separador **Grupos & utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de actualização'.**

- Tenha a atualização do utilizador da extensão do navegador do painel de acesso seguindo as etapas abaixo na secção de extensão do Navegador do [Painel de Acesso.](#how-to-install-the-access-panel-browser-extension)

- Certifique-se de que a extensão do navegador do painel de acesso está a decorrer e está ativada no navegador do utilizador.

- Certifique-se de que os seus utilizadores não estão a tentar iniciar sessão na aplicação a partir do painel de acesso enquanto estiverem **no modo incógnito, em privado ou no modo Privado**. A extensão do painel de acesso não é suportada nestes modos.

Caso as sugestões anteriores não funcionem, pode ser que tenha ocorrido uma alteração do lado da aplicação que tenha quebrado temporariamente a integração do pedido com a AD Azure. Por exemplo, isto pode ocorrer quando o fornecedor de aplicações introduz um script na sua página que se comporta de forma diferente para entrada manual vs automatizada, o que faz com que a integração automatizada, como a nossa, se rompa. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicações para resolver rapidamente estes problemas.

Embora a Microsoft tenha tecnologias para detetar automaticamente quando as integrações de aplicações se rompem, pode não ser possível encontrar os problemas imediatamente, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de suporte para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-lhes** o nosso caminho para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o vendedor para a Listagem da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](../azuread-dev/howto-app-gallery-listing.md) para começar.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e Firefox, mas não no Internet Explorer

Há duas principais causas para esta questão:

- Dependendo das definições de segurança ativadas no Internet Explorer, se o website não fizer parte de uma **Zona Fidedigna**, por vezes o nosso script será bloqueado da execução para a aplicação.

  *  Para resolver isto, instrua o utilizador a **adicionar o website da aplicação** à lista de Sites **Fidedignos** dentro das suas **definições**de segurança do Internet Explorer . Pode enviar os seus utilizadores para o Como adicionar um site ao meu artigo de lista de [sites confiáveis](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) para instruções detalhadas.

- Em circunstâncias raras, a validação de segurança do Internet Explorer pode, por vezes, fazer com que a página carregue mais lentamente do que a execução do nosso script.

  * Infelizmente, esta situação pode variar dependendo da versão do navegador, velocidade do computador ou site visitado. Neste caso, sugerimos que contacte o apoio para que possamos corrigir a integração para esta aplicação específica.

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-lhes** o nosso caminho para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o vendedor para a Listagem da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](../azuread-dev/howto-app-gallery-listing.md) para começar.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de login da aplicação mudou recentemente ou requer um campo adicional

Se a página de login da aplicação mudou drasticamente, por vezes isso faz com que as nossas integrações se rompam. Um exemplo disso é quando um fornecedor de aplicações adiciona um campo de inscrição, uma captcha ou uma autenticação multifactor às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicações para resolver rapidamente estes problemas.

Embora a Microsoft tenha tecnologias para detetar automaticamente quando as integrações de aplicações se rompem, pode não ser possível encontrar os problemas imediatamente, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de suporte para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-lhes** o nosso caminho para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o vendedor para a Listagem da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](../azuread-dev/howto-app-gallery-listing.md) para começar.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga os passos abaixo:

1.  Abra o Painel de [Acesso](https://myapps.microsoft.com) num dos navegadores suportados e inscreva-se como **utilizador** no seu Azure AD.

2.  clique numa **aplicação password-SSO** no Painel de Acesso.

3.  Na solicitação imediata de instalar o software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você é direcionado para o link de descarregamento. **Adicione** a extensão ao seu navegador.

5.  Se o seu navegador perguntar, selecione para **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reinicie** a sua sessão de navegador.

7.  Inicie sessão no Painel de Acesso e veja se pode **lançar** as suas aplicações password-SSO

Também pode descarregar a extensão para Chrome e Firefox a partir dos links diretos abaixo:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)

