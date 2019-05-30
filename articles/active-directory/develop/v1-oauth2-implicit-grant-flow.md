---
title: Noções básicas sobre o OAuth2 implícita conceder o fluxo no Azure AD | Documentos da Microsoft
description: Saiba mais sobre a implementação do Azure Active Directory de OAuth2 implícita concedem o fluxo, e se é adequada para a sua aplicação.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe0ee8021ae7e70654a161e37d072195bbc035f
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545244"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure Active Directory (AD)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

A concessão implícita OAuth2 é famosa por ser a concessão com a mais longa lista de preocupações com a segurança na especificação do OAuth2. E ainda, que é a abordagem implementada pelo JS da ADAL e o que recomendamos ao escrever aplicativos SPA. O que acontece? É todos os uma questão de vantagens e desvantagens: e acontece que, a concessão implícita é a melhor abordagem, pode buscar para aplicações que consomem uma API Web por meio de JavaScript a partir de um browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita OAuth2?

O súbita [concessão do código de autorização de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) é a concessão de autorização que utiliza dois pontos de extremidade separados. Ponto final de autorização é utilizado para a fase de interação do utilizador, o que resulta num código de autorização. Ponto final do token, em seguida, é utilizado pelo cliente para trocar o código de um token de acesso e, muitas vezes, um token de atualização também. Aplicativos Web são necessários para apresentar suas credenciais de aplicativo para o ponto final do token, para que o servidor de autorização pode autenticar o cliente.

O [concessão implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outras concessões de autorização. Ele permite que um cliente obter um token de acesso (e id_token, ao usar [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) diretamente a partir do ponto de final de autorização, sem entrar em contato com o ponto final do token nem autenticar o cliente. Essa variante foi projetado para o JavaScript com base em aplicações em execução num navegador da Web: na especificação do OAuth2 original, os tokens são retornados num fragmento do URI. Que disponibiliza os bits de token para o código JavaScript no cliente, mas garante, não incluídos no redireciona para o servidor. Obter tokens vracející via redirecionamentos do navegador diretamente a partir do ponto final de autorização. Ele também tem a vantagem de eliminação de quaisquer requisitos para cruzada chamadas de origem, que são necessárias se a aplicação do JavaScript é necessário para contactar o ponto final do token.

Uma característica importante do que a concessão implícita OAuth2 é o fato de que tais fluem de tokens de atualização nunca retorno para o cliente. A secção seguinte mostra como isso não é necessário e seria, na verdade, um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A especificação do OAuth2 declara que a concessão implícita tem de ser pensada para permitir que aplicativos de agente do usuário – ou seja, aplicativos de JavaScript em execução dentro de um navegador. O traço que define desses aplicativos é que o código JavaScript é utilizado para aceder a recursos de servidor (normalmente, uma API Web) e para atualizar a experiência de utilizador de aplicação em conformidade. Pense em aplicações como o Gmail ou Outlook Web Access: Quando seleciona uma mensagem de sua caixa de entrada, apenas o painel de visualização da mensagem é alterado para apresentar a nova seleção, enquanto o resto da página permanece inalterado. Essa característica está em contraste com tradicionais baseada em redireccionamento aplicações Web, onde cada interação do utilizador resulta num postback de página completa e um processamento de página completa da nova resposta do servidor.

Aplicativos que utilize a abordagem de baseados em JavaScript para extrema são chamados de aplicativos de única página, ou de SPAs. A idéia é que esses aplicativos têm apenas uma página inicial do HTML e JavaScript associada, com todas as interações subsequentes sendo direcionado por chamadas de Web API realizado por meio de JavaScript. No entanto, as abordagens híbridas, onde o aplicativo é principalmente orientada por postback mas efetua chamadas JS ocasionais, não são invulgares – a discussão sobre a utilização de fluxo implícito é relevante para as pessoas também.

Aplicativos baseada em redireccionamento seguro, normalmente, os pedidos através de cookies, no entanto, que abordagem não funciona bem para aplicativos JavaScript. Cookies só funcionam com o domínio que ter sido gerados, enquanto as chamadas de JavaScript podem ser direcionadas para outros domínios. Na verdade, que muitas vezes será o caso: Pense em aplicações que invocam a API do Azure do Microsoft Graph API, a API do Office, – todos os que residem fora do domínio a partir de onde a aplicação é enviada. Uma tendência crescente para aplicações JavaScript é ter sem back-end, da entidade confiadora 100% no terceiros APIs da Web para implementar a sua função de negócio.

Atualmente, o método preferencial de proteger as chamadas para uma API Web é usar a abordagem token de portador o OAuth2, onde todas as chamadas é acompanhada de um token de acesso de OAuth2. A API Web examina o token de acesso de entrada e, se encontra no mesmo os âmbitos necessários, ele concede acesso para a operação pedida. O fluxo implícito fornece um mecanismo prático para aplicativos de JavaScript para obter os tokens de acesso para uma API Web, oferecendo diversas vantagens em respeito ao cookies:

* Tokens podem ser obtidos fiável sem a necessidade de cross origin chamadas – registo obrigatório de redirecionamento URI para os quais tokens são retornados garante que os tokens não são moveu
* Aplicativos de JavaScript podem obter os tokens de acesso que precisam para todas as APIs da Web destinam-se – sem restrição de domínios
* Recursos do HTML5, como a sessão ou armazenamento local conceder controle total sobre tokens em cache e o gerenciamento da vida útil, ao passo que o gerenciamento de cookies é opaco para a aplicação
* Tokens de acesso não estão suscetíveis a ataques de forjada (CSRF) de solicitação entre sites

O fluxo de concessão implícita não emitirá tokens de atualização, principalmente por motivos de segurança. Um token de atualização não é tão estreitamente no escopo como tokens de acesso, conceder muito mais poder inflicting, por conseguinte, muito mais danos no caso de ele ocorreu uma fuga da. O fluxo implícito, tokens são entregues no URL, portanto o risco de interceptação é maior do que na concessão do código de autorização.

No entanto, uma aplicação de JavaScript tem outro mecanismo à sua disposição para renovar os tokens de acesso sem repetidamente pedir ao utilizador as credenciais. O aplicativo pode usar um iframe oculto para realizar novos pedidos de token relativamente ao ponto final de autorização do Azure AD:, desde que o navegador ainda tem uma sessão ativa (ler: tem um cookie de sessão) contra o domínio do Azure AD, o pedido de autenticação pode ocorra com êxito sem a necessidade de interação do utilizador.

Esse modelo concede a aplicação do JavaScript a capacidade de forma independente renovar os tokens de acesso e até mesmo adquirir novos para uma nova API (desde que o anteriormente consentidas por utilizadores para os mesmos). Isso evita a carga a mais ao adquirir, manter e proteger um artefato de valor elevado, como um token de atualização. O artefacto que possibilita a renovação automática, o cookie de sessão do Azure AD, é gerido fora do aplicativo. Outra vantagem dessa abordagem é que um utilizador pode terminar sessão no Azure AD, com qualquer um dos aplicativos tem sessão iniciados no Azure AD, em execução em qualquer um dos separadores de browser. Isso resulta na eliminação do cookie de sessão do Azure AD e o aplicativo de JavaScript automaticamente perderá a capacidade de renovar os tokens para o utilizador assinado horizontalmente.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequado para a minha aplicação?

A concessão implícita apresenta riscos de mais do que outros concede e as áreas que precisa prestar atenção a sejam bem documentadas (por exemplo, [utilização indevida de Token de acesso para representar o proprietário de recursos no fluxo implícito] [ OAuth2-Spec-Implicit-Misuse]e [modelo de risco de OAuth 2.0 e considerações de segurança][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco mais alto é amplamente devido ao fato de que o principal objetivo é permitir que os aplicativos que executam código Active Directory, servido por um recurso remoto para um browser. Se estiver a planear uma arquitetura SPA, nenhum componente de back-end de tenham ou pretendam invocar uma API Web por meio de JavaScript, recomenda-se a utilização do fluxo implícito para aquisição do token.

Se seu aplicativo for um cliente nativo, o fluxo implícito não é uma excelente escolha. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo deprives a aplicação a partir do meio de manutenção de uma sessão de longa duração. Que significa que seu aplicativo repetidamente pedirá ao utilizador ao obter os tokens de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclui um back-end e uma API a partir do seu código de back-end de consumo, o fluxo implícito também não é uma boa opção. Outros concede-lhe muito mais energia. Por exemplo, a concessão de credenciais de cliente de OAuth2 fornece a capacidade de obter os tokens que refletem as permissões atribuídas para o próprio aplicativo, em vez das delegações do utilizador. Isso significa que o cliente tem a capacidade de manter o acesso programático a recursos, mesmo quando um utilizador não está ativamente envolvido numa sessão e assim por diante. Não é só isso, mas essas concessões dar garantias de segurança mais elevadas. Por exemplo, tokens de acesso nunca em trânsito por meio do navegador do usuário, não o risco de que está a ser guardadas no histórico do navegador e assim por diante. O aplicativo cliente também pode efetuar uma autenticação segura ao pedir um token.

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma lista completa de recursos para desenvolvedores, incluindo informações de referência para os protocolos e a autorização de OAuth2 concedem fluxos suporte pelo Azure AD, consulte o [Guia do programador do Azure AD][AAD-Developers-Guide]
* Ver [como integrar uma aplicação com o Azure AD] [ ACOM-How-To-Integrate] para profundidade adicional sobre o processo de integração de aplicativo.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
