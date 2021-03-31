---
title: Compreender o fluxo de subvenção implícita OAuth2 em Azure AD | Microsoft Docs
description: Saiba mais sobre a implementação do fluxo de subvenção implícita OAuth2 da Azure Ative, e se é adequado para a sua aplicação.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154513"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Compreender o fluxo de subvenção implícita OAuth2 no Azure Ative Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A subvenção implícita OAuth2 é notória por ser a subvenção com a maior lista de preocupações de segurança na especificação OAuth2. No entanto, esta é a abordagem implementada pela ADAL JS e aquela que recomendamos ao escrever aplicações de SPA. O que dá? É tudo uma questão de trocas: e, como se vê, a concessão implícita é a melhor abordagem que pode seguir para aplicações que consomem uma API web via JavaScript a partir de um navegador.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita da OAuth2?

A [concessão de código de autorização OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) quintessencial é a concessão de autorização que utiliza dois pontos finais distintos. O ponto final de autorização é utilizado para a fase de interação do utilizador, o que resulta num código de autorização. O ponto final simbólico é então usado pelo cliente para trocar o código por um token de acesso, e muitas vezes um token de atualização também. As aplicações web são necessárias para apresentar as suas próprias credenciais de aplicação no ponto final do token, para que o servidor de autorização possa autenticar o cliente.

A [subvenção implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outras subvenções de autorização. Permite que um cliente obtenha um token de acesso (e id_token, ao utilizar o [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) diretamente do ponto final de autorização, sem contactar o ponto final do token nem autenticar o cliente. Esta variante foi concebida para aplicações baseadas em JavaScript que estão a decorrer num navegador Web: na especificação original de OAuth2, os tokens são devolvidos num fragmento URI. Isto torna os bits simbólicos disponíveis para o código JavaScript no cliente, mas garante que não serão incluídos em redirecionamentos para o servidor. Na concessão implícita da OAuth2, o ponto final de autorização emite acesso diretamente ao cliente usando um URI de redirecionamento que foi previamente fornecido. Também tem a vantagem de eliminar quaisquer requisitos para chamadas de origem cruzada, que são necessários se a aplicação JavaScript for necessária para entrar em contato com o ponto final simbólico.

Uma característica importante da subvenção implícita OAuth2 é o facto de tais fluxos nunca devolverem fichas de atualização ao cliente. A próxima secção mostra como isto não é necessário e seria, de facto, um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a subvenção implícita OAuth2

A especificação OAuth2 declara que a subvenção implícita foi concebida para permitir aplicações de agente de utilizador – isto é, aplicações JavaScript executadas dentro de um browser. A característica determinante de tais aplicações é que o código JavaScript é usado para aceder a recursos do servidor (normalmente uma API web) e para atualizar a experiência do utilizador da aplicação em conformidade. Pense em aplicações como o Gmail ou o Outlook Web Access: quando seleciona uma mensagem na sua caixa de entrada, apenas o painel de visualização de mensagens muda para exibir a nova seleção, enquanto o resto da página permanece desmodificado. Esta característica contrasta com as aplicações web tradicionais baseadas em redirecionamento, onde cada interação do utilizador resulta num postback de página inteira e numa renderização de página inteira da nova resposta do servidor.

As aplicações que tomam a abordagem baseada em JavaScript para o seu extremo são chamadas aplicações de uma página única, ou SPAs. A ideia é que estas aplicações apenas sirvam uma página html inicial e JavaScript associado, com todas as interações subsequentes sendo conduzidas por chamadas web API realizadas via JavaScript. No entanto, as abordagens híbridas, em que a aplicação é maioritariamente orientada para o pós-retrocesso, mas realiza chamadas ocasionais de JS, não são incomuns – a discussão sobre o uso implícito do fluxo também é relevante para aqueles.

As aplicações baseadas em redirecionamento normalmente asseguram os seus pedidos através de cookies, no entanto, essa abordagem não funciona tão bem para aplicações JavaScript. Os cookies apenas funcionam contra o domínio para o quais foram gerados, enquanto as chamadas JavaScript podem ser direcionadas para outros domínios. De facto, isso será frequentemente o caso: pense em aplicações que invocam a Microsoft Graph API, Office API, Azure API – todas residentes fora do domínio de onde a aplicação é servida. Uma tendência crescente para aplicações JavaScript é não ter qualquer backend, contando 100% em APIs Web de terceiros para implementar a sua função de negócio.

Atualmente, o método preferido para proteger as chamadas para uma API web é utilizar a abordagem simbólica portadora de OAuth2, onde cada chamada é acompanhada por um token de acesso OAuth2. A API Web examina o token de acesso de entrada e, se encontrar nele os âmbitos necessários, concede acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para aplicações JavaScript para obter tokens de acesso para uma API Web, oferecendo inúmeras vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma fiável sem necessidade de chamadas de origem cruzada – registo obrigatório do URI redirecionado para o qual os tokens são devolução garantias de que os tokens não são deslocados
* As aplicações JavaScript podem obter o número de tokens de acesso que precisarem, para o maior número de APIs web que visam – sem restrições em domínios
* As funcionalidades HTML5, como sessão ou armazenamento local, concedem controlo total sobre o caching token e a gestão vitalícia, enquanto a gestão de cookies é opaca para a app
* Os tokens de acesso não são suscetíveis a ataques de falsificação de pedidos de cross-site (CSRF)

O fluxo implícito de subvenções não emite fichas de atualização, principalmente por razões de segurança. Um token de atualização não é tão alargado como os tokens de acesso, concedendo muito mais energia, portanto, infligindo muito mais danos no caso de ser vazado. No fluxo implícito, os tokens são entregues na URL, pelo que o risco de interceção é maior do que no código de autorização.

No entanto, uma aplicação JavaScript tem outro mecanismo à sua disposição para renovar fichas de acesso sem solicitar repetidamente ao utilizador credenciais. A aplicação pode usar um iframe oculto para realizar novos pedidos simbólicos contra o ponto final de autorização do Azure AD: desde que o navegador ainda tenha uma sessão ativa (leia-se: tem um cookie de sessão) contra o domínio Azure AD, o pedido de autenticação pode ocorrer com sucesso sem qualquer necessidade de interação do utilizador.

Este modelo confere à aplicação JavaScript a capacidade de renovar independentemente os tokens de acesso e até adquirir novos para uma nova API (desde que o utilizador tenha previamente consentido por eles). Isto evita o fardo adicional de adquirir, manter e proteger um artefacto de alto valor, como um token de atualização. O artefacto que torna possível a renovação silenciosa, o cookie de sessão Azure AD, é gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode assinar a partir do Azure AD, utilizando qualquer uma das aplicações assinadas no AZure AD, correndo em qualquer um dos separadores do navegador. Isto resulta na eliminação do cookie de sessão Ad Azure, e a aplicação JavaScript perderá automaticamente a capacidade de renovar fichas para o utilizador assinado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A subvenção implícita é adequada para a minha aplicação?

A subvenção implícita apresenta mais riscos do que outras subvenções, e as áreas a que deve prestar atenção estão bem documentadas (por exemplo, [utilização indevida de Token de Acesso a Proprietário de Recursos Imitadores em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse] e [OAuth 2.0 Modelo de Ameaça e Considerações de Segurança).][OAuth2-Threat-Model-And-Security-Implications] No entanto, o maior perfil de risco deve-se em grande parte ao facto de se destinar a ativar aplicações que executam código ativo, servido por um recurso remoto para um navegador. Se estiver a planear uma arquitetura SPA, não tiver componentes de backend ou pretender invocar uma API web via JavaScript, recomenda-se a utilização do fluxo implícito para a aquisição de token.

Se a sua aplicação é um cliente nativo, o fluxo implícito não é um grande ajuste. A ausência do cookie de sessão Azure AD no contexto de um cliente nativo priva a sua aplicação dos meios de manter uma sessão de longa duração. O que significa que a sua aplicação irá repetidamente incitar o utilizador ao obter fichas de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclua um backend e a consumir uma API a partir do seu código backend, o fluxo implícito também não é um bom ajuste. Outras subvenções dão-lhe muito mais poder. Por exemplo, a concessão de credenciais de clienteS OAuth2 proporciona a capacidade de obter fichas que reflitam as permissões atribuídas à própria aplicação, em oposição às delegações de utilizadores. Isto significa que o cliente tem a capacidade de manter o acesso programático aos recursos mesmo quando um utilizador não está ativamente envolvido numa sessão, e assim por diante. Não só isso, mas tais subvenções dão garantias de segurança mais elevadas. Por exemplo, os tokens de acesso nunca passam pelo navegador do utilizador, não correm o risco de serem guardados no histórico do navegador, e assim por diante. A aplicação do cliente também pode realizar autenticação forte ao solicitar um token.

## <a name="next-steps"></a>Passos seguintes

* Veja [como integrar uma aplicação com a Azure AD][ACOM-How-To-Integrate] para uma profundidade adicional no processo de integração da aplicação.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
