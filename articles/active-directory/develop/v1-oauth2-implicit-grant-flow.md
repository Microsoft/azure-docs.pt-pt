---
title: Noções básicas sobre o fluxo de concessão implícita do OAuth2 no Azure AD | Microsoft Docs
description: Saiba mais sobre a implementação de Azure Active Directory do fluxo de concessão implícita OAuth2 e se é adequado para seu aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e30bd940d3312a16f2dd30b175deb6622cb8c01
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834741"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure Active Directory (AD)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

A concessão implícita OAuth2 é evidente para ser a concessão com a lista mais longa de preocupações de segurança na especificação OAuth2. E, ainda, essa é a abordagem implementada pelo ADAL JS e a que recomendamos ao escrever aplicativos SPA. O que dá? É uma questão de compensações: e como acontece, a concessão implícita é a melhor abordagem que você pode buscar para aplicativos que consomem uma API Web via JavaScript de um navegador.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita OAuth2?

A [concessão de código de autorização OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) então é a concessão de autorização que usa dois pontos de extremidade separados. O ponto de extremidade de autorização é usado para a fase de interação do usuário, o que resulta em um código de autorização. O ponto de extremidade do token é usado pelo cliente para trocar o código para um token de acesso e, geralmente, um token de atualização também. Os aplicativos Web são necessários para apresentar suas próprias credenciais de aplicativo para o ponto de extremidade do token, para que o servidor de autorização possa autenticar o cliente.

A [concessão implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outras concessões de autorização. Ele permite que um cliente obtenha um token de acesso (e id_token, ao usar o [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)) diretamente do ponto de extremidade de autorização, sem entrar em contato com o ponto de extremidade do token nem autenticar o cliente. Essa variante foi criada para aplicativos baseados em JavaScript em execução em um navegador da Web: na especificação OAuth2 original, os tokens são retornados em um fragmento de URI. Isso torna os bits de token disponíveis para o código JavaScript no cliente, mas garante que eles não serão incluídos em redirecionamentos para o servidor. Retornando tokens por meio de redirecionamentos de navegador diretamente do ponto de extremidade de autorização. Ele também tem a vantagem de eliminar quaisquer requisitos para chamadas entre origens, que são necessárias se o aplicativo JavaScript for necessário para entrar em contato com o ponto de extremidade do token.

Uma característica importante da concessão implícita OAuth2 é o fato de que esses fluxos nunca retornam tokens de atualização para o cliente. A próxima seção mostra como isso não é necessário e, na verdade, seria um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A especificação OAuth2 declara que a concessão implícita foi criada para habilitar aplicativos de agente do usuário – ou seja, aplicativos JavaScript em execução em um navegador. A característica que define esses aplicativos é que o código JavaScript é usado para acessar recursos do servidor (normalmente uma API da Web) e para atualizar a experiência do usuário do aplicativo de acordo. Imagine aplicativos como Gmail ou Outlook Acesso via Web: quando você seleciona uma mensagem da caixa de entrada, somente o painel de visualização de mensagem é alterado para exibir a nova seleção, enquanto o restante da página permanece sem modificações. Essa característica está em contraste com os aplicativos Web tradicionais baseados em redirecionamento, em que cada interação do usuário resulta em um postback de página inteira e uma renderização de página completa da nova Resposta do servidor.

Os aplicativos que usam a abordagem baseada em JavaScript para seu extremo são chamados de aplicativos de página única ou SPAs. A ideia é que esses aplicativos só servem uma página HTML inicial e JavaScript associado, com todas as interações subsequentes sendo controladas por chamadas à API Web executadas via JavaScript. No entanto, as abordagens híbridas, em que o aplicativo é basicamente controlado por postback, mas executa chamadas JS ocasionais, não são incomuns – a discussão sobre o uso de fluxo implícito também é relevante para esses.

Os aplicativos baseados em redirecionamento normalmente protegem suas solicitações por meio de cookies, no entanto, essa abordagem não funciona bem para aplicativos JavaScript. Os cookies só funcionam no domínio para o qual foram gerados, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. Na verdade, esse será o caso: Considere os aplicativos que chamam Microsoft Graph API, a API do Office, a API do Azure – todos que residem fora do domínio de onde o aplicativo é servido. Uma tendência crescente para aplicativos JavaScript é não ter nenhum back-end, dependendo de 100% em APIs da Web de terceiros para implementar sua função comercial.

Atualmente, o método preferencial de proteção de chamadas para uma API da Web é usar a abordagem de token de portador OAuth2, em que cada chamada é acompanhada por um token de acesso OAuth2. A API da Web examina o token de acesso de entrada e, se ele encontrar os escopos necessários, ele concederá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para que aplicativos JavaScript obtenham tokens de acesso para uma API da Web, oferecendo inúmeras vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma confiável sem a necessidade de chamadas entre origens – o registro obrigatório do URI de redirecionamento para o qual os tokens são retornados garante que os tokens não sejam inseridos
* Os aplicativos JavaScript podem obter tantos tokens de acesso quantos forem necessários, para tantas APIs Web direcionadas – sem restrição de domínios
* Os recursos do HTML5, como sessão ou armazenamento local, concedem controle total sobre cache de token e gerenciamento de tempo de vida, enquanto o gerenciamento de cookies é opaco para o aplicativo
* Tokens de acesso não são suscetíveis a ataques CSRF (solicitação entre sites forjada)

O fluxo de concessão implícita não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não tem um escopo restrito como tokens de acesso, concedendo muito mais energia, provocando muito mais danos caso ele seja vazado. No fluxo implícito, os tokens são entregues na URL, portanto, o risco de interceptação é maior do que na concessão de código de autorização.

No entanto, um aplicativo JavaScript tem outro mecanismo em sua disposição para renovar tokens de acesso sem solicitar repetidamente as credenciais ao usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token no ponto de extremidade de autorização do Azure AD: desde que o navegador ainda tenha uma sessão ativa (leitura: tem um cookie de sessão) no domínio do Azure AD, a solicitação de autenticação pode ocorre com êxito sem a necessidade de interação do usuário.

Esse modelo concede ao aplicativo JavaScript a capacidade de renovar de forma independente os tokens de acesso e até mesmo adquirir novos para uma nova API (desde que o usuário tenha consentido anteriormente). Isso evita o ônus adicional de adquirir, manter e proteger um artefato de valor alto, como um token de atualização. O artefato que torna a renovação silenciosa possível, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem é que um usuário pode sair do Azure AD, usando qualquer um dos aplicativos conectados ao Azure AD, em execução em qualquer uma das guias do navegador. Isso resulta na exclusão do cookie de sessão do Azure AD, e o aplicativo JavaScript perderá automaticamente a capacidade de renovar tokens para o usuário desconectado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequada para meu aplicativo?

A concessão implícita apresenta mais riscos do que outras concessões, e as áreas que você precisa prestar atenção são bem documentadas (por exemplo, o [uso indevido do token de acesso para representar o proprietário do recurso no fluxo implícito][OAuth2-Spec-Implicit-Misuse] e o [modelo de ameaça OAuth 2,0 e segurança Considerações][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco mais alto é basicamente devido ao fato de que ele é destinado a habilitar aplicativos que executam código ativo, servido por um recurso remoto para um navegador. Se você estiver planejando uma arquitetura SPA, não tiver componentes de back-end ou pretender invocar uma API Web via JavaScript, recomenda-se o uso do fluxo implícito para aquisição de token.

Se seu aplicativo for um cliente nativo, o fluxo implícito não será uma ótima opção. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo priva seu aplicativo do meio de manter uma sessão de vida longa. Ou seja, o aplicativo solicitará repetidamente o usuário ao obter tokens de acesso para novos recursos.

Se você estiver desenvolvendo um aplicativo Web que inclui um back-end e consumindo uma API de seu código de back-end, o fluxo implícito também não é uma boa opção. Outras concessões oferecem muito mais energia. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em oposição às delegações de usuário. Isso significa que o cliente tem a capacidade de manter o acesso programático a recursos mesmo quando um usuário não está ativamente envolvido em uma sessão e assim por diante. Não só isso, mas tais concessões oferecem garantias de segurança mais altas. Por exemplo, os tokens de acesso nunca passam pelo navegador do usuário, eles não têm o risco de serem salvos no histórico do navegador e assim por diante. O aplicativo cliente também pode executar uma autenticação forte ao solicitar um token.

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma lista completa de recursos de desenvolvedor, incluindo informações de referência para os protocolos e suporte a fluxos de concessão de autorização OAuth2 pelo Azure AD, consulte o [Guia do desenvolvedor do Azure ad][AAD-Developers-Guide]
* Consulte [como integrar um aplicativo com o Azure ad][ACOM-How-To-Integrate] para obter mais detalhes sobre o processo de integração de aplicativos.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
