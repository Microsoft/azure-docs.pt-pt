---
title: Compreender o fluxo implícito de subvenção Da OAuth2 em Azure AD [ Microsoft Docs
description: Saiba mais sobre a implementação do fluxo de subvenção implícita o OAuth2 da Azure Ative Directory e se é adequado para a sua aplicação.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154513"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Compreender o fluxo implícito de subvenção OAuth2 no Diretório Ativo azure (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A subvenção implícita da OAuth2 é notória por ser a subvenção com a lista mais longa de preocupações de segurança na especificação OAuth2. No entanto, esta é a abordagem implementada pela ADAL JS e a que recomendamos ao escrever aplicações de SPA. O que dá? É tudo uma questão de trocas: e, ao que parece, a subvenção implícita é a melhor abordagem que pode procurar para aplicações que consomem uma Web API via JavaScript a partir de um browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a subvenção implícita o OAuth2?

A bolsa de código de [autorização OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) é a concessão de autorização que utiliza dois pontos finais distintos. O ponto final de autorização é utilizado para a fase de interação do utilizador, o que resulta num código de autorização. O ponto final simbólico é então usado pelo cliente para trocar o código por um token de acesso, e muitas vezes também um token refrescante. As aplicações web são necessárias para apresentar as suas próprias credenciais de aplicação ao ponto final simbólico, para que o servidor de autorização possa autenticar o cliente.

A [subvenção implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outras subvenções de autorização. Permite que um cliente obtenha um token de acesso (e id_token, ao utilizar o [OpenId Connect)](https://openid.net/specs/openid-connect-core-1_0.html)diretamente a partir do ponto final de autorização, sem contactar o ponto final simbólico nem autenticar o cliente. Esta variante foi projetada para aplicações baseadas em JavaScript que executam num navegador Web: na especificação original do OAuth2, os tokens são devolvidos num fragmento de URI. Isso disponibiliza os bits simbólicos para o código JavaScript no cliente, mas garante que não serão incluídos em redirecionamentos para o servidor. Na concessão implícita da OAuth2, a autorização endpoint emite fichas de acesso diretamente ao cliente utilizando um URI redirecionado que foi previamente fornecido. Também tem a vantagem de eliminar quaisquer requisitos para chamadas de origem cruzada, que são necessárias se a aplicação JavaScript for necessária para contactar o ponto final do símbolo.

Uma característica importante da subvenção implícita OAuth2 é o facto de tais fluxos nunca devolverem fichas refrescantes ao cliente. A próxima secção mostra como isto não é necessário e seria, de facto, um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a subvenção implícita OAuth2

A especificação OAuth2 declara que a subvenção implícita foi concebida para permitir aplicações de agente de utilizador – ou seja, aplicações JavaScript executadas dentro de um browser. A característica determinante de tais aplicações é que o código JavaScript é usado para aceder aos recursos do servidor (tipicamente um API Web) e para atualizar a experiência do utilizador da aplicação em conformidade. Pense em aplicações como o Gmail ou o Outlook Web Access: quando selecionar uma mensagem da sua caixa de entrada, apenas o painel de visualização de mensagens muda para exibir a nova seleção, enquanto o resto da página permanece inalterado. Esta característica contrasta com as aplicações Web tradicionais baseadas em redireciones, onde cada interação do utilizador resulta num postback de página inteira e numa renderização de página inteira da nova resposta do servidor.

As aplicações que tomam a abordagem baseada no JavaScript ao seu extremo são chamadas aplicações de página única, ou SPAs. A ideia é que estas aplicações apenas sirvam uma página inicial de HTML e javaScript associado, com todas as interações subsequentes sendo conduzidas por chamadas Web API realizadas via JavaScript. No entanto, as abordagens híbridas, em que a aplicação é maioritariamente orientada para o pós-recuo, mas executam chamadas js ocasionais, não são incomuns – a discussão sobre o uso implícito do fluxo também é relevante para aqueles.

As aplicações baseadas em redirecionamento normalmente asseguram os seus pedidos através de cookies, no entanto, essa abordagem não funciona tão bem para aplicações JavaScript. Os cookies só funcionam contra o domínio para o que foram gerados, enquanto as chamadas JavaScript podem ser direcionadas para outros domínios. De facto, isso será frequentemente o caso: pense em aplicações que invocam a Microsoft Graph API, Office API, Azure API – todos residentes fora do domínio de onde a aplicação é servida. Uma tendência crescente para as aplicações JavaScript é não ter qualquer backend, confiando 100% em APIs web de terceiros para implementar a sua função de negócio.

Atualmente, o método preferido de proteger chamadas para uma API Web é usar a abordagem token do portador OAuth2, onde cada chamada é acompanhada por um sinal de acesso OAuth2. A Web API examina o sinal de acesso de entrada e, se encontrar nele os âmbitos necessários, dá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para as aplicações JavaScript obterem fichas de acesso para uma API Web, oferecendo inúmeras vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma fiável sem qualquer necessidade de chamadas de origem cruzada – registo obrigatório do URI redirecionado para o qual as fichas são de retorno garante que as fichas não são deslocadas
* As aplicações JavaScript podem obter o máximo de fichas de acesso que precisarem, para o maior número de APIs web que visam – sem restrições em domínios
* Características HTML5 como sessão ou bolsa de armazenamento local concedem controlo total sobre o caching simbólico e a gestão vitalícia, enquanto a gestão de cookies é opaca para a app
* Fichas de acesso não são suscetíveis a ataques de falsificação de pedido de cross-site (CSRF)

O fluxo implícito de subvenção não emite fichas de atualização, principalmente por razões de segurança. Um token refrescante não é tão estreitamente traçado como fichas de acesso, concedendo muito mais energia, permitindo muito mais danos no caso de ser vazado. No fluxo implícito, as fichas são entregues no URL, daí que o risco de interceção seja maior do que na concessão do código de autorização.

No entanto, uma aplicação JavaScript tem outro mecanismo à sua disposição para renovar fichas de acesso sem solicitar repetidamente ao utilizador credenciais. A aplicação pode usar um iframe oculto para realizar novos pedidos simbólicos contra o ponto final de autorização do Azure AD: desde que o navegador ainda tenha uma sessão ativa (leia-se: tem um cookie de sessão) contra o domínio DaA Azure, o pedido de autenticação pode ocorre ndo com sucesso sem qualquer necessidade de interação do utilizador.

Este modelo concede à aplicação JavaScript a capacidade de renovar independentemente os tokens de acesso e até adquirir novos para uma nova API (desde que o utilizador previamente consentiu por eles). Isto evita o fardo acrescido de adquirir, manter e proteger um artefacto de alto valor, como um símbolo de atualização. O artefacto que torna possível a renovação silenciosa, o cookie de sessão Azure AD, é gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode assinar a partir do Azure AD, utilizando qualquer uma das aplicações assinadas no Azure AD, funcionando em qualquer um dos separadores do navegador. Isto resulta na eliminação do cookie de sessão Azure AD, e a aplicação JavaScript perderá automaticamente a capacidade de renovar fichas para o utilizador assinado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A subvenção implícita é adequada para a minha aplicação?

A subvenção implícita apresenta mais riscos do que outras subvenções, e as áreas a que precisa de estar atento estão bem documentadas (por exemplo, [utilização indevida de acesso ao Proprietário de Recursos Imitadores em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse] e [OAuth 2.0 Modelo][OAuth2-Threat-Model-And-Security-Implications]de Ameaça e Considerações de Segurança). No entanto, o perfil de risco mais elevado deve-se, em grande parte, ao facto de se destinar a ativar aplicações que executam código ativo, servidos por um recurso remoto para um navegador. Se estiver a planear uma arquitetura SPA, não tenha componentes de backend ou pretenda invocar um Web API via JavaScript, recomenda-se a utilização do fluxo implícito para aquisição de fichas.

Se a sua aplicação é um cliente nativo, o fluxo implícito não é um grande ajuste. A ausência do cookie de sessão Azure AD no contexto de um cliente nativo priva a sua aplicação dos meios de manter uma sessão de longa duração. O que significa que a sua aplicação irá incitar repetidamente o utilizador a obter fichas de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclua um backend, e a consumir uma API do seu código de backend, o fluxo implícito também não é um bom ajuste. Outras subvenções dão-lhe muito mais poder. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter fichas que reflitam as permissões atribuídas à própria aplicação, ao contrário das delegações dos utilizadores. Isto significa que o cliente tem a capacidade de manter o acesso programático aos recursos mesmo quando um utilizador não está ativamente envolvido numa sessão, e assim por diante. Não só isso, mas tais subvenções dão garantias de segurança mais elevadas. Por exemplo, os tokens de acesso nunca transitam através do navegador de utilizador, não correm o risco de ser guardados no histórico do navegador, e assim por diante. A aplicação do cliente também pode realizar uma autenticação forte ao solicitar um símbolo.

## <a name="next-steps"></a>Passos seguintes

* Ver Como integrar uma aplicação com a [Azure AD][ACOM-How-To-Integrate] para obter uma profundidade adicional no processo de integração de aplicações.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
