---
title: Aplicações de Web API no Azure Active Directory
description: Descreve o que são aplicações de API web e as noções básicas sobre fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f344a4f928030c07f59c2f7aba201d153da123f1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918720"
---
# <a name="web-api"></a>API Web

Aplicações de API da Web são aplicativos web que tem de obter recursos de uma API web. Neste cenário, existem dois tipos de identidade que o aplicativo web pode utilizar para autenticar e chamar a API web:

- **Identidade da aplicação** -este cenário utiliza a concessão de credenciais de cliente OAuth 2.0 para autenticar-se que a aplicação e aceder a API web. Quando utilizar uma identidade de aplicação, a web API só pode detetar que o aplicativo web é chamá-la, como o web API não recebeu qualquer informação sobre o utilizador. Se o aplicativo recebe informações sobre o utilizador, este será enviado através do protocolo de aplicação e não está assinado pelo Azure AD. A API web confia que o aplicativo web autenticar o utilizador. Por esse motivo, esse padrão é chamado de um subsistema confiável.
- **Delegado a identidade do usuário** -neste cenário pode ser feito de duas formas: OpenID Connect e concessão de código de autorização de OAuth 2.0 com um cliente confidencial. A aplicação web obtém um token de acesso para o utilizador, o que comprova a web API que o utilizador autenticado com êxito para a aplicação web e de que a aplicação web foi capaz de obter uma identidade de utilizador delegado para chamar a API web. Esse token de acesso é enviado no pedido para a web API, que autoriza o usuário e retorna o recurso pretendido.

A identidade da aplicação e os tipos de identidade do utilizador delegado são abordados no fluxo abaixo. A principal diferença entre eles é que a identidade de utilizador delegado deve primeiramente adquirir um código de autorização antes do utilizador pode iniciar sessão e obter acesso para a API web.

## <a name="diagram"></a>Diagrama

![Aplicação Web para o diagrama da Web API](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder a identidade da aplicação com credenciais de cliente OAuth 2.0

1. Um utilizador tem sessão iniciado para o Azure AD na aplicação web (consulte a **aplicações Web** secção para obter mais informações).
1. A aplicação web tem de adquirir um token de acesso, para que possa autenticar para a API web e recuperar o recurso pretendido. Ele faz um pedido ao ponto final de token do Azure AD, fornecendo a credencial, o ID da aplicação e o URI de ID da aplicação da API web.
1. O Azure AD autentica o aplicativo e devolve um token de acesso do JWT que é utilizado para chamar a API web.
1. Através de HTTPS, a aplicação web utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem-sucedida, retorna o recurso pretendido.

### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de utilizador delegado com OpenID Connect

1. Um usuário está conectado a uma aplicação web com o Azure AD (veja a secção de aplicativo Web acima do Browser). Se o utilizador da aplicação web não consentiu ainda para permitir que a aplicação web para chamar a API web em seu nome, o utilizador terá de dar consentimento. A aplicação irá apresentar as permissões requer e, se qualquer uma dessas permissões ao nível do administrador, um usuário normal no diretório não será capaz de dar consentimento. Este processo de consentimento só se aplica a aplicações multi-inquilino, aplicações de inquilino não único, como o aplicativo já terá as permissões necessárias. Quando o usuário conectado, a aplicação web foi recebido um token de ID com informações sobre o utilizador, bem como um código de autorização.
1. Usando o código de autorização emitido pelo Azure AD, o aplicativo web envia um pedido ao ponto final de token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso desejado (ID da aplicação URI para a API web).
1. O código de autorização e as informações sobre o aplicativo web e a web API são validadas pelo Azure AD. Após a validação com êxito, o Azure AD devolve dois tokens: um token de acesso do JWT e um token de atualização do JWT.
1. Através de HTTPS, a aplicação web utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem-sucedida, retorna o recurso pretendido.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de utilizador delegado com a concessão do código de autorização de OAuth 2.0

1. Um utilizador já tiver sessão iniciado aplicativo web, cujo mecanismo de autenticação é independente do Azure AD.
1. O aplicativo web requer um código de autorização para adquirir um token de acesso, portanto, ele emite um pedido por meio do navegador para o ponto final de autorização do Azure AD, fornecendo o ID da aplicação e URI de redirecionamento para a aplicação web após a autenticação com êxito. O utilizador inicia sessão com o Azure AD.
1. Se o utilizador da aplicação web não consentiu ainda para permitir que a aplicação web para chamar a API web em seu nome, o utilizador terá de dar consentimento. A aplicação irá apresentar as permissões requer e, se qualquer uma dessas permissões ao nível do administrador, um usuário normal no diretório não será capaz de dar consentimento. Este consentimento aplica-se a aplicação de única e multi-inquilino. No caso do inquilino único, um administrador pode executar o consentimento para consentimento de administrador em nome dos seus utilizadores. Isso pode ser feito com o `Grant Permissions` botão no [portal do Azure](https://portal.azure.com). 
1. Depois do utilizador consentiu, o aplicativo web recebe o código de autorização que necessita para adquirir um token de acesso.
1. Usando o código de autorização emitido pelo Azure AD, o aplicativo web envia um pedido ao ponto final de token do Azure AD que inclui o código de autorização, detalhes sobre a aplicação de cliente (ID da aplicação e URI de redirecionamento) e o recurso desejado (ID da aplicação URI para a API web).
1. O código de autorização e as informações sobre o aplicativo web e a web API são validadas pelo Azure AD. Após a validação com êxito, o Azure AD devolve dois tokens: um token de acesso do JWT e um token de atualização do JWT.
1. Através de HTTPS, a aplicação web utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem-sucedida, retorna o recurso pretendido.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código para a aplicação Web para cenários de Web API. E, visite-a freqüentemente--novos exemplos são adicionados com frequência. Web [aplicativo Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registo de aplicações

Registar uma aplicação com o ponto de final de versão 1.0 do Azure AD, consulte [registar uma aplicação](quickstart-register-app.md).

* Inquilino único, para a identidade da aplicação e casos de identidade do utilizador delegado, a aplicação web e a API web têm de estar registado no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizadas para limitar o acesso da aplicação web para seus recursos. Se está a ser utilizado um tipo de identidade do utilizador delegado, a aplicação web tem de selecionar as permissões pretendidas a partir da **permissões para outras aplicações** menu pendente no portal do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.
* Multi-inquilino-em primeiro lugar, a aplicação web está configurado para indicar as permissões que ele requer que seja funcional. Esta lista de permissões necessárias é mostrada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino dá consentimento para a aplicação, o que torna disponível para a sua organização. Apenas algumas aplicações necessitam de permissões de nível de usuário, que pode consentir qualquer utilizador na organização. Outras aplicações requerem permissões de nível de administrador, que não pode autorizar um utilizador na organização. Apenas um administrador do diretório pode dar consentimento a aplicações que exigem esse nível de permissões. Quando o utilizador ou administrador autorizar, o aplicativo web e a API web são ambos registados no respetivo diretório.

## <a name="token-expiration"></a>Expiração do token

Quando a aplicação web utiliza o seu código de autorização ao obter o token de acesso de um JWT, também recebe um token de atualização do JWT. Quando o token de acesso expira, o token de atualização pode ser utilizado para autenticar o usuário sem a necessidade de iniciar sessão novamente. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre outras [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre o Azure AD [Noções básicas de autenticação](authentication-scenarios.md)
