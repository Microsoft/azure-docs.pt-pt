---
title: Como lidar com a Proteção inteligente de rastreio (ITP) no Safari Rio Azure
titleSuffix: Microsoft identity platform
description: Autenticação de aplicações de uma página única (SPA) quando os cookies de terceiros já não são permitidos.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 064c9a00e1cd7c139f3f42a053dcf8a5db13f161
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104585"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Lide com o ITP no Safari e noutros navegadores onde os cookies de terceiros estão bloqueados

Muitos navegadores hoje em dia estão bloqueando cookies de terceiros - cookies em pedidos para domínios que não são os mesmos que estão na barra de navegador. Isto quebra o fluxo implícito e requer novos padrões de autenticação para assinar com sucesso nos utilizadores. Na plataforma de identidade da Microsoft, utilizamos o fluxo de autorização com PKCE e atualizamos tokens para manter os utilizadores assinados quando os cookies de terceiros são bloqueados.

## <a name="what-is-intelligent-tracking-protection-itp"></a>O que é a Intelligent Tracking Protection (ITP)?

O Apple Safari tem uma funcionalidade de proteção de privacidade on-by-by-default chamada [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/), ou *ITP*. O ITP bloqueia cookies de "terceiros", cookies em pedidos que cruzam domínios.

Uma forma comum de rastreio do utilizador é feita carregando um iframe para site de terceiros em segundo plano e usando cookies para correlacionar o utilizador através da Internet. Infelizmente, este padrão é também a forma padrão de implementar o [fluxo implícito](v2-oauth2-implicit-grant-flow.md) em aplicações de uma só página (SPAs). Quando um navegador bloqueia cookies de terceiros para impedir o rastreio do utilizador, as SPAs também são quebradas.

O Safari não é o único a bloquear cookies de terceiros para melhorar a privacidade dos utilizadores. A Brave bloqueou por padrão os cookies de terceiros e a Chromium (a plataforma por trás do Google Chrome e Microsoft Edge) anunciou que também deixarão de suportar cookies de terceiros no futuro.

A solução delineada neste artigo funciona em todos estes navegadores, ou em qualquer lugar que os cookies de terceiros estejam bloqueados.

## <a name="overview-of-the-solution"></a>Visão geral da solução

Para continuar a autenticar os utilizadores em SPAs, os desenvolvedores de aplicações devem utilizar o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md). No fluxo de código auth, o fornecedor de identidade emite um código, e o SPA resgata o código para um token de acesso e um token de atualização. Quando a aplicação requer fichas adicionais, pode usar o [fluxo de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) para obter novos tokens. MSAL.js 2.0, a biblioteca da plataforma de identidade da Microsoft para SPAs, implementa o fluxo de código de autorização para SPAs e, com pequenas atualizações, é uma substituição de entrada para MSAL.js 1.x.

Para a plataforma de identidade da Microsoft, as SPAs e os clientes nativos seguem orientações de protocolo semelhantes:

* Utilização de um desafio de [código PKCE](https://tools.ietf.org/html/rfc7636)
    * O PKCE é *necessário* para spAs na plataforma de identidade da Microsoft. O PKCE é *recomendado* para clientes nativos e confidenciais.
* Sem uso de um segredo de cliente

As SPAs têm duas restrições adicionais:

* [O URI de redirecionamento `spa` deve ser marcado como tipo](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) para ativar o CORS nos pontos finais de login.
* Os tokens de atualização emitidos através do fluxo de código de autorização para `spa` redirecionar URIs têm uma vida útil de 24 horas em vez de uma vida útil de 90 dias.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagrama que mostra o fluxo de código de autorização OAuth 2 entre uma aplicação de uma página e o ponto final do serviço de ficha de segurança." border="false":::

## <a name="performance-and-ux-implications"></a>Implicações de desempenho e UX

Algumas aplicações que utilizam o login implícito da tentativa de fluxo sem redirecionar abrindo um iframe de login utilizando `prompt=none` . Na maioria dos navegadores, este pedido irá responder com fichas para o utilizador atualmente inscrito (assumindo que o consentimento já foi concedido). Este padrão significava que as aplicações não precisavam de um redirecionamento de página inteira para iniciar a sação do utilizador, melhorando o desempenho e a experiência do utilizador - o utilizador visita a página web e já está assinado. Uma `prompt=none` vez que num iframe já não é uma opção quando os cookies de terceiros são bloqueados, as aplicações devem visitar a página de login num quadro de alto nível para ter um código de autorização emitido.

Há duas formas de conseguir o sign-in:

* **Redirecionamentos de página inteira**
    * Na primeira carga do SPA, redirecione o utilizador para a página de início de sessão se já não existir uma sessão (ou se a sessão tiver expirado). O navegador do utilizador visitará a página de login, apresentará os cookies que contêm a sessão do utilizador e, em seguida, redirecionará de volta para a aplicação com o código e fichas num fragmento.
    * O redirecionamento resulta em que o SPA seja carregado duas vezes. Siga as melhores práticas para o caching de SPAs para que a aplicação não seja descarregada na totalidade duas vezes.
    * Considere ter uma sequência de pré-carregamento na aplicação que verifica uma sessão de login e redireciona para a página de login antes que a aplicação desembale totalmente e execute a carga útil do JavaScript.
* **Popups**
    * Se a experiência do utilizador (UX) de um redirecionamento de página inteira não funcionar para a aplicação, considere usar um popup para lidar com a autenticação.
    * Quando o popup terminar de redirecionar para a aplicação após a autenticação, o código no manipulador de redirecionamento armazenará o código e as fichas no armazenamento local para a aplicação a utilizar. MSAL.js suporta popups para autenticação, assim como a maioria das bibliotecas.
    * Os navegadores estão a diminuir o suporte para popups, por isso podem não ser a opção mais confiável. A interação do utilizador com o SPA antes de criar o popup pode ser necessária para satisfazer os requisitos do navegador.

>[!NOTE]
> A Apple [descreve um método popup](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) como uma correção de compatibilidade temporária para dar à janela original acesso a cookies de terceiros. Embora a Apple possa remover esta transferência de permissões no futuro, não terá impacto na orientação aqui. Aqui, o popup está a ser usado como uma primeira navegação partidária para a página de login para que uma sessão seja encontrada e um código de auth pode ser fornecido. Isto deve continuar a trabalhar no futuro.

### <a name="a-note-on-iframe-apps"></a>Uma nota sobre aplicativos iframe

Um padrão comum nas aplicações web é usar um iframe para incorporar uma aplicação dentro de outra. As pegas de quadro de nível superior que autenticam o utilizador, e a aplicação alocorda no iframe pode confiar que o utilizador está assinado, recolhendo tokens silenciosamente usando o fluxo implícito. A aquisição de token silenciosa já não funciona quando os cookies de terceiros são bloqueados - a aplicação incorporada no iframe deve mudar para usar popups para aceder à sessão do utilizador, uma vez que não pode navegar para a página de login.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Implicações de segurança de fichas de atualização no navegador

A emissão de fichas de atualização para o navegador é considerada um problema de segurança. Os ataques de scripts de sites cruzados (XSS) ou pacotes JS comprometidos podem roubar o token da atualização e usá-lo remotamente até que expire ou seja revogado. Para minimizar o risco de tokens de atualização roubados, as SPAs serão emitidas fichas válidas por apenas 24 horas. Após 24 horas, a aplicação deve adquirir um novo código de autorização através de uma visita de quadro de alto nível à página de login.

Este padrão de token de renovação de vida limitada foi escolhido como um equilíbrio entre segurança e UX degradado. Sem tokens de atualização ou cookies de terceiros, o fluxo de código de autorização (conforme recomendado pelo [projeto de boas práticas de segurança OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) torna-se onerosa quando são necessárias fichas novas ou adicionais. Um redirecionamento de página inteira ou popup é necessário para cada token, cada vez que um token expira (a cada hora normalmente, para fichas de plataforma de identidade da Microsoft).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o fluxo de [código de autorização.](v2-oauth2-auth-code-flow.md)

Experimente o fluxo de código de autorização com o [MSAL.js quickstart 2.0](quickstart-v2-javascript-auth-code.md).
