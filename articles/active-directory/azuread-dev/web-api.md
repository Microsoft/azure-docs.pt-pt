---
title: Aplicativos Web API em Diretório Ativo Azure
description: Descreve quais são as aplicações web da API e o básico no fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154428"
---
# <a name="web-api"></a>API Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

As aplicações web API são aplicações web que precisam de obter recursos de uma API web. Neste cenário, existem dois tipos de identidade que a aplicação web pode usar para autenticar e chamar a API web:

- **Identidade de aplicação** - Este cenário utiliza credenciais de cliente OAuth 2.0 para autenticar como aplicação e aceder à API web. Ao utilizar uma identidade de aplicação, a API web só pode detetar que a aplicação web está a chamá-la, uma vez que a API web não recebe qualquer informação sobre o utilizador. Se a aplicação receber informações sobre o utilizador, esta será enviada através do protocolo de aplicação, e não é assinada pela Azure AD. A API web confia que a aplicação web autenticou o utilizador. Por esta razão, este padrão é chamado de subsistema de confiança.
- **Identidade de utilizador delegada** - Este cenário pode ser realizado de duas formas: OpenID Connect e OAuth 2.0 autorização código com um cliente confidencial. A aplicação web obtém um token de acesso para o utilizador, o que comprova à API web que o utilizador autenticou com sucesso a aplicação web e que a aplicação web foi capaz de obter uma identidade de utilizador delegada para ligar para a API web. Este token de acesso é enviado no pedido para a API web, que autoriza o utilizador e devolve o recurso pretendido.

Tanto a identidade da aplicação como os tipos de identidade de utilizador delegados são discutidos no fluxo abaixo. A principal diferença entre eles é que a identidade de utilizador delegada deve primeiro adquirir um código de autorização antes que o utilizador possa iniciar sedutor e ter acesso à API web.

## <a name="diagram"></a>Diagrama

![Aplicação web para diagrama de API web](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade de aplicação com bolsa de credenciais de cliente OAuth 2.0

1. Um utilizador é inscrito no Azure AD na aplicação web (consulte a secção **de aplicações Web** para obter mais informações).
1. A aplicação web precisa adquirir um token de acesso para que possa autenticar a API web e recuperar o recurso pretendido. Faz um pedido ao ponto final simbólico da Azure AD, fornecendo a credencial, iD de aplicação e iD iD de aplicação da Web API.
1. A Azure AD autentica a aplicação e devolve um token de acesso JWT que é usado para chamar a API web.
1. Em HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho de autorização do pedido à API web. A API web valida então o token JWT, e se a validação for bem sucedida, devolve o recurso pretendido.

### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de utilizador delegada com OpenID Connect

1. Um utilizador é inscrito numa aplicação web utilizando a Azure AD (consulte a secção Web Browser para Aplicação Web acima). Se o utilizador da aplicação web ainda não tiver concordado em permitir que a aplicação web ligue para a API web em seu nome, o utilizador terá de consentir. A aplicação apresentará as permissões necessárias e, se alguma delas for permissões de nível de administrador, um utilizador normal no diretório não poderá consentir. Este processo de consentimento aplica-se apenas a pedidos de multi-inquilinos, e não a pedidos de inquilino único, uma vez que o pedido já terá as permissões necessárias. Quando o utilizador se inscreveu, a aplicação web recebeu um token de identificação com informações sobre o utilizador, bem como um código de autorização.
1. Utilizando o código de autorização emitido pela Azure AD, a aplicação web envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID URI de aplicação para a API web).
1. O código de autorização e informações sobre a aplicação web e a API web são validados pela Azure AD. Após a validação bem sucedida, a Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Em HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho de autorização do pedido à API web. A API web valida então o token JWT, e se a validação for bem sucedida, devolve o recurso pretendido.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade delegada do utilizador com bolsa de código de autorização OAuth 2.0

1. Um utilizador já está inscrito numa aplicação web, cujo mecanismo de autenticação é independente do Azure AD.
1. A aplicação web requer um código de autorização para adquirir um token de acesso, pelo que emite um pedido através do navegador para o ponto final de autorização da Azure AD, fornecendo o ID da aplicação e redirecionando URI para a aplicação web após a autenticação bem sucedida. O utilizador assina no Azure AD.
1. Se o utilizador da aplicação web ainda não tiver concordado em permitir que a aplicação web ligue para a API web em seu nome, o utilizador terá de consentir. A aplicação apresentará as permissões necessárias e, se alguma delas for permissões de nível de administrador, um utilizador normal no diretório não poderá consentir. Este consentimento aplica-se tanto à aplicação de solteiros como de vários inquilinos. No caso de um único inquilino, um administrador pode realizar o consentimento administrativo para consentir em nome dos seus utilizadores. Isto pode ser feito utilizando o `Grant Permissions` botão no [portal Azure](https://portal.azure.com). 
1. Depois de o utilizador ter consentido, a aplicação web recebe o código de autorização de que necessita para adquirir um token de acesso.
1. Utilizando o código de autorização emitido pela Azure AD, a aplicação web envia um pedido ao ponto final simbólico da Azure AD que inclui o código de autorização, detalhes sobre a aplicação do cliente (ID de aplicação e redirecionamento URI), e o recurso pretendido (ID URI de aplicação para a API web).
1. O código de autorização e informações sobre a aplicação web e a API web são validados pela Azure AD. Após a validação bem sucedida, a Azure AD devolve dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Em HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho de autorização do pedido à API web. A API web valida então o token JWT, e se a validação for bem sucedida, devolve o recurso pretendido.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para os cenários da Web Application para web API. E, verifique com frequência, novas amostras são adicionadas frequentemente. Aplicação Web [para API Web.](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity)

## <a name="app-registration"></a>Registo de aplicações

Para registar uma aplicação no ponto final Azure AD v1.0, consulte [registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Inquilino único - Tanto para a identidade da aplicação como para casos de identidade de utilizador delegado, a aplicação web e a API web devem ser registadas no mesmo diretório em Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso da aplicação web aos seus recursos. Se estiver a ser utilizado um tipo de identidade de utilizador delegado, a aplicação web precisa de selecionar as permissões desejadas das **Permissões para outras aplicações** no portal Azure. Este passo não é necessário se o tipo de identidade de aplicação estiver a ser utilizado.
* Multi-inquilino - Primeiro, a aplicação web está configurada para indicar as permissões que necessita para ser funcional. Esta lista de permissões necessárias é mostrada num diálogo quando um utilizador ou administrador no diretório de destino dá consentimento à aplicação, o que a coloca à disposição da sua organização. Algumas aplicações apenas requerem permissões ao nível do utilizador, que qualquer utilizador da organização pode consentir. Outras aplicações requerem permissões ao nível do administrador, que um utilizador na organização não pode consentir. Apenas um administrador de diretório pode dar consentimento a aplicações que requerem este nível de permissões. Quando o utilizador ou administrador consente, a aplicação web e a API web estão registadas no seu diretório.

## <a name="token-expiration"></a>Expiração simbólica

Quando a aplicação web utiliza o seu código de autorização para obter um token de acesso JWT, também recebe um token de atualização JWT. Quando o token de acesso expirar, o token de atualização pode ser usado para reautenticar o utilizador sem que eles voltem a fazer o seu sing. Esta ficha de atualização é então usada para autenticar o utilizador, o que resulta num novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos e cenários de aplicações](app-types.md)
- Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação AD do Azure
