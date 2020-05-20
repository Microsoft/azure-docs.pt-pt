---
title: Como lidar com a Proteção Inteligente de Rastreio (ITP) no Safari [ Azure
titleSuffix: Microsoft identity platform
description: Autenticação de aplicações de uma só página (SPA) quando os cookies de terceiros já não são permitidos.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: cf385ef9af152308bcd96f25df49aebddd25b059
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691062"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Manuseie o ITP no Safari e outros navegadores onde os cookies de terceiros são bloqueados

Muitos navegadores de hoje estão bloqueando cookies de terceiros - cookies em pedidos para domínios que não são os mesmos que os que aparecem na barra de navegador. Isto quebra o fluxo implícito e requer novos padrões de autenticação para assinar com sucesso nos utilizadores. Na plataforma de identidade da Microsoft, utilizamos o fluxo de autorização com o PKCE e atualizamos tokens para manter os utilizadores assinados quando os cookies de terceiros estão bloqueados.

## <a name="what-is-intelligent-tracking-protection-itp"></a>O que é a Proteção Inteligente de Rastreio (ITP)?

O Apple Safari tem uma funcionalidade de proteção de privacidade por defeito chamada [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/), ou *ITP*. ItP bloqueia cookies de "terceiros", cookies em pedidos que cruzam domínios.

Uma forma comum de rastreio do utilizador é feita carregando um iframe para site de terceiros em segundo plano e usando cookies para correlacionar o utilizador através da Internet. Infelizmente, este padrão é também a forma padrão de implementar o [fluxo implícito](v2-oauth2-implicit-grant-flow.md) em aplicações de uma só página (SPAs). Quando um navegador bloqueia cookies de terceiros para impedir o rastreio do utilizador, os SPAs também estão avariados.

O Safari não é o único a bloquear cookies de terceiros para melhorar a privacidade dos utilizadores. A Brave bloqueou cookies de terceiros por padrão, e a Chromium (a plataforma por trás do Google Chrome e Microsoft Edge) anunciou que também vão deixar de suportar cookies de terceiros no futuro.

A solução delineada neste artigo funciona em todos estes navegadores, ou em qualquer lugar que os cookies de terceiros estejam bloqueados.

## <a name="overview-of-the-solution"></a>Visão geral da solução

Para continuar a autenticar os utilizadores em SPAs, os desenvolvedores de aplicações devem utilizar o fluxo de [código de autorização](v2-oauth2-auth-code-flow.md). No fluxo de código auth, o fornecedor de identidade emite um código, e o SPA resgata o código para um token de acesso e um token de atualização. Quando a aplicação requer tokens adicionais, pode usar o fluxo de token de [atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) para obter novos tokens. MSAL.js 2.0, a biblioteca da plataforma de identidade Microsoft para SPAs, implementa o fluxo de código de autorização para SPAs e, com pequenas atualizações, é uma substituição de entrada para MSAL.js 1.x.

Para a plataforma de identidade da Microsoft, As SPAs e os clientes nativos seguem orientações de protocolo semelhantes:

* Utilização de um desafio de [código PKCE](https://tools.ietf.org/html/rfc7636)
    * O PKCE é *necessário* para SPAs na plataforma de identidade da Microsoft. O PKCE é *recomendado* para clientes nativos e confidenciais.
* Não usar um segredo de cliente

As SPAs têm duas restrições adicionais:

* [O URI redirecionado deve `spa` ser marcado como tipo](v2-oauth2-auth-code-flow.md#setup-required-for-single-page-apps) para ativar o CORS nos pontos finais de login.
* As fichas de atualização emitidas através do fluxo de código de autorização para `spa` redirecionar os URIs têm uma vida útil de 24 horas em vez de uma vida útil de 90 dias.

![Fluxo de código para aplicativos SPA](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

## <a name="performance-and-ux-implications"></a>Implicações de desempenho e UX

Algumas aplicações que utilizam o log-in implícito sem redirecionar a abertura de um iframe de login utilizando `prompt=none` . Na maioria dos navegadores, este pedido responderá com fichas para o utilizador atualmente assinado (assumindo que o consentimento já foi concedido). Este padrão significava que as aplicações não precisavam de um redirecionamento de página inteira para iniciar sessão, melhorando o desempenho e a experiência do utilizador - o utilizador visita a página web e já está assinado. Como `prompt=none` num iframe já não é uma opção quando os cookies de terceiros são bloqueados, as aplicações devem visitar a página de login num quadro de alto nível para ter um código de autorização emitido.

Há duas formas de realizar o sign-in:

* **Redirecionamentos de página inteira**
    * Na primeira carga do SPA, redirecione o utilizador para a página de início de sessão se não existir nenhuma sessão (ou se a sessão estiver caducada). O navegador do utilizador visitará a página de login, apresentará os cookies que contêm a sessão do utilizador e, em seguida, redirecionará para a aplicação com o código e tokens num fragmento.
    * O redirecionamento resulta na carga do SPA duas vezes. Siga as melhores práticas para o caching de SPAs para que a aplicação não seja descarregada na íntegra duas vezes.
    * Considere ter uma sequência de pré-carga na aplicação que verifica para uma sessão de login e redireciona para a página de login antes que a aplicação desfaça as malas e execute a carga útil JavaScript.
* **Popups**
    * Se a experiência do utilizador (UX) de um redirecionamento de página inteira não funcionar para a aplicação, considere utilizar um popup para lidar com a autenticação.
    * Quando o popup terminar a redirecionamento para a aplicação após a autenticação, o código no manipulador de redirecionamento armazenará o código e fichas no armazenamento local para a aplicação usar. MSAL.js apoia popups para autenticação, assim como a maioria das bibliotecas.
    * Os navegadores estão a diminuir o suporte para popups, por isso podem não ser a opção mais confiável. A interação do utilizador com o SPA antes de criar o popup pode ser necessária para satisfazer os requisitos do navegador.

>[!NOTE]
> A Apple [descreve um método popup](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) como uma correção temporária de compatibilidade para dar acesso original à janela a cookies de terceiros. Embora a Apple possa remover esta transferência de permissões no futuro, não terá impacto na orientação aqui. Aqui, o popup está sendo usado como uma primeira navegação de festa para a página de login para que uma sessão seja encontrada e um código auth pode ser fornecido. Isto deve continuar a trabalhar no futuro.

### <a name="a-note-on-iframe-apps"></a>Uma nota sobre aplicações iframe

Um padrão comum em aplicações web é usar um iframe para incorporar uma aplicação dentro de outra. O quadro de alto nível autenticando o utilizador, e a aplicação hospedada no iframe pode confiar que o utilizador está inscrito, obtendo tokens silenciosamente usando o fluxo implícito. A aquisição de fichasilenciosa silenciosa já não funciona quando os cookies de terceiros são bloqueados - a aplicação incorporada no iframe deve mudar para usar popups para aceder à sessão do utilizador, uma vez que não pode navegar para a página de login.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Implicações de segurança de fichas de atualização no navegador

A emissão de fichas de atualização para o navegador é considerada um problema de segurança. Os ataques de scripts transversais (XSS) ou os pacotes JS comprometidos podem roubar o token de atualização e usá-lo remotamente até expirar ou ser revogado. A fim de minimizar o risco de fichas de atualização roubadas, serão emitidos tokens válidos por apenas 24 horas. Após 24 horas, a aplicação deve adquirir um novo código de autorização através de uma visita de quadro de alto nível à página de login.

Este padrão de token de atualização de vida limitada foi escolhido como um equilíbrio entre segurança e UX degradado. Sem fichas de atualização ou cookies de terceiros, o fluxo de código de autorização (conforme recomendado pelo projeto de [práticas atuais de segurança OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) torna-se onerosa quando são necessários novos ou adicionais tokens. Um redirecionamento de página inteira ou popup é necessário para cada token, cada vez que um token expira (normalmente, a cada hora, para fichas da plataforma de identidade da Microsoft).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o fluxo de [código de autorização.](v2-oauth2-auth-code-flow.md)

Experimente o fluxo de código de autorização com o [MSAL.js 2.0 quickstart](quickstart-v2-javascript-auth-code.md).
