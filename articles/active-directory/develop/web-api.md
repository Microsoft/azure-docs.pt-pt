---
title: Aplicativos Web API no Diretório Ativo azure
description: Descreve quais são as aplicações web da API e os fundamentos sobre o fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: e57e027848294cbff570cb64d0ad4bbf05693ffe
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699806"
---
# <a name="web-api"></a>API Web

Aplicações Web API são aplicações web que precisam de obter recursos de uma API web. Neste cenário, existem dois tipos de identidade que a aplicação web pode usar para autenticar e chamar a Web API:

- **Identidade de aplicação** - Este cenário utiliza credenciais de cliente OAuth 2.0 para autenticar como aplicação e aceder à Web API. Ao utilizar uma identidade de aplicação, a API web só pode detetar que a aplicação web está a chamá-la, uma vez que a API web não recebe qualquer informação sobre o utilizador. Se a aplicação receber informações sobre o utilizador, será enviada através do protocolo de aplicação e não é assinada pela Azure AD. A Web API confia que a aplicação web autenticou o utilizador. Por esse motivo, esse padrão é chamado de um subsistema confiável.
- **Identidade de utilizador delegada** - Este cenário pode ser realizado de duas formas: OpenID Connect e OAuth 2.0 com um cliente confidencial. A aplicação web obtém um sinal de acesso para o utilizador, o que comprova à Web API que o utilizador autenticou com sucesso à aplicação web e que a aplicação web foi capaz de obter uma identidade de utilizador delegada para ligar para a Web API. Este token de acesso é enviado no pedido para a Web API, que autoriza o utilizador e devolve o recurso pretendido.

Tanto a identidade da aplicação como os tipos de identidade de utilizador delegados são discutidos no fluxo abaixo. A diferença fundamental entre eles é que a identidade de utilizador delegada deve primeiro adquirir um código de autorização antes de o utilizador poder iniciar sessão e ter acesso à API web.

## <a name="diagram"></a>Diagrama

![Aplicação web para diagrama de API web](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade de candidatura com bolsa de credenciais de cliente OAuth 2.0

1. Um utilizador é inscrito no Azure AD na aplicação web (ver a secção de **aplicações Web** para obter mais informações).
1. A aplicação web tem de adquirir um token de acesso, para que possa autenticar para a API web e recuperar o recurso pretendido. Faz um pedido ao ponto final simbólico da Azure AD, fornecendo a credencial, id de aplicação e id de aplicação da Web API.
1. A Azure AD autentica a aplicação e devolve um token de acesso JWT que é usado para chamar a Web API.
1. Ao longo do HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho de autorização do pedido à Web API. A Web API valida então o símbolo JWT, e se a validação for bem sucedida, devolve o recurso desejado.

### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de utilizador delegada com OpenID Connect

1. Um utilizador é inscrito numa aplicação web utilizando a AD Azure (ver a secção Web Browser para aplicação web acima). Se o utilizador da aplicação web ainda não tiver consentido em permitir que a aplicação web ligue para a Web API em seu nome, o utilizador terá de consentir. A aplicação apresentará as permissões necessárias e, se alguma delas for em permissões de nível de administrador, um utilizador normal no diretório não poderá consentir. Este processo de consentimento aplica-se apenas a pedidos de multi-inquilinos, e não a pedidos de inquilino único, uma vez que o pedido já terá as permissões necessárias. Quando o utilizador inscreveu, a aplicação web recebeu um documento de identificação com informações sobre o utilizador, bem como um código de autorização.
1. Utilizando o código de autorização emitido pela Azure AD, a aplicação web envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID de aplicação) URI para a Web API).
1. O código de autorização e informações sobre a aplicação web e a Web API são validados pela Azure AD. Após uma validação bem sucedida, a Azure AD devolve duas fichas: um símbolo de acesso JWT e um token de atualização JWT.
1. Ao longo do HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho de autorização do pedido à Web API. A Web API valida então o símbolo JWT, e se a validação for bem sucedida, devolve o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de utilizador delegada com concessão de código de autorização OAuth 2.0

1. Um utilizador já está inscrito numa aplicação web, cujo mecanismo de autenticação é independente da Azure AD.
1. A aplicação web requer um código de autorização para adquirir um token de acesso, pelo que emite um pedido através do navegador para o ponto final de autorização da Azure AD, fornecendo o ID de aplicação e redirecionando o URI para a aplicação web após a autenticação bem sucedida. O utilizador entra em anúncio de Azure.
1. Se o utilizador da aplicação web ainda não tiver consentido em permitir que a aplicação web ligue para a Web API em seu nome, o utilizador terá de consentir. A aplicação apresentará as permissões necessárias e, se alguma delas for em permissões de nível de administrador, um utilizador normal no diretório não poderá consentir. Este consentimento aplica-se tanto ao pedido de inquilino único como a vários inquilinos. No caso único do inquilino, um administrador pode executar o consentimento do administrador para consentir em nome dos seus utilizadores. Isto pode ser feito utilizando o botão `Grant Permissions` no [portal Azure](https://portal.azure.com). 
1. Depois de o utilizador ter consentido, a aplicação web recebe o código de autorização que necessita para adquirir um token de acesso.
1. Utilizando o código de autorização emitido pela Azure AD, a aplicação web envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID de aplicação) URI para a Web API).
1. O código de autorização e informações sobre a aplicação web e a Web API são validados pela Azure AD. Após uma validação bem sucedida, a Azure AD devolve duas fichas: um símbolo de acesso JWT e um token de atualização JWT.
1. Ao longo do HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho de autorização do pedido à Web API. A Web API valida então o símbolo JWT, e se a validação for bem sucedida, devolve o recurso desejado.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para aplicação web para cenários de API web. E, volte frequentemente- novas amostras são adicionadas frequentemente. Aplicação Web [para Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registo da aplicação

Para registar uma aplicação no ponto final da AD Azure, consulte [O Registo de uma aplicação](quickstart-register-app.md).

* Inquilino único - Tanto para a identidade de aplicação como para os casos de identidade de utilizador delegados, a aplicação web e a API web devem ser registadas no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso da aplicação web aos seus recursos. Se estiver a ser utilizado um tipo de identidade de utilizador delegado, a aplicação web tem de selecionar as permissões desejadas das **Permissões para outras aplicações** no portal Azure. Este passo não é necessário se o tipo de identidade da aplicação estiver a ser utilizado.
* Multi-inquilino - Em primeiro lugar, a aplicação web está configurada para indicar as permissões que necessita para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões de nível de administrador, que um utilizador da organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a pedidos que requerem este nível de permissões. Quando o utilizador ou administrador consentir, a aplicação web e a API web estão ambos registados no seu diretório.

## <a name="token-expiration"></a>Expiração do token

Quando a aplicação web usa o seu código de autorização para obter um sinal de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem exigir que eles voltem a iniciar sessão. Este token de atualização é então usado para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários](app-types.md) de aplicação
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação da AD Azure
