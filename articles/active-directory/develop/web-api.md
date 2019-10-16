---
title: Aplicativos de API Web no Azure Active Directory
description: Descreve quais aplicativos de API da Web são e os conceitos básicos sobre o fluxo de protocolo, o registro e a expiração do token para esse tipo de aplicativo.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4babb7e869f4fc83bcdb530a580a29dda234293
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373785"
---
# <a name="web-api"></a>API Web

Os aplicativos de API Web são aplicativos Web que precisam obter recursos de uma API da Web. Nesse cenário, há dois tipos de identidade que o aplicativo Web pode usar para autenticar e chamar a API da Web:

- **Identidade do aplicativo** -esse cenário usa a concessão de credenciais de cliente do OAuth 2,0 para autenticar como o aplicativo e acessar a API da Web. Ao usar uma identidade de aplicativo, a API da Web só pode detectar que o aplicativo Web a está chamando, pois a API Web não recebe nenhuma informação sobre o usuário. Se o aplicativo receber informações sobre o usuário, ele será enviado por meio do protocolo de aplicativo e não será assinado pelo Azure AD. A API da Web confia que o aplicativo Web autenticou o usuário. Por esse motivo, esse padrão é chamado de subsistema confiável.
- **Identidade de usuário delegado** -esse cenário pode ser realizado de duas maneiras: OpenID Connect e concessão de código de autorização OAuth 2,0 com um cliente confidencial. O aplicativo Web Obtém um token de acesso para o usuário, que comprova a API da Web que o usuário autenticou com êxito para o aplicativo Web e que o aplicativo Web pôde obter uma identidade de usuário delegada para chamar a API da Web. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

A identidade do aplicativo e os tipos de identidade do usuário delegado são discutidos no fluxo abaixo. A principal diferença entre eles é que a identidade do usuário delegado deve primeiro adquirir um código de autorização antes que o usuário possa entrar e obter acesso à API da Web.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo Web para API da Web](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais de cliente OAuth 2,0

1. Um usuário está conectado ao Azure AD no aplicativo Web (consulte a seção **aplicativos Web** para obter mais informações).
1. O aplicativo Web precisa adquirir um token de acesso para que ele possa se autenticar na API Web e recuperar o recurso desejado. Ele faz uma solicitação para o ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do aplicativo e o URI da ID do aplicativo da API Web.
1. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. Em seguida, a API da Web valida o token JWT e, se a validação for bem-sucedida, retornará o recurso desejado.

### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de usuário delegado com OpenID Connect

1. Um usuário está conectado a um aplicativo Web usando o Azure AD (consulte a seção navegador da Web para aplicativo Web acima). Se o usuário do aplicativo Web ainda não tiver consentido que permita que o aplicativo Web chame a API da Web em seu nome, o usuário precisará consentir. O aplicativo exibirá as permissões necessárias e, se alguma delas for de nível de administrador, um usuário normal no diretório não poderá consentir. Esse processo de consentimento se aplica somente a aplicativos multilocatários, não a aplicativos de locatário único, pois o aplicativo já terá as permissões necessárias. Quando o usuário se conectou, o aplicativo Web recebeu um token de ID com informações sobre o usuário, bem como um código de autorização.
1. Usando o código de autorização emitido pelo Azure AD, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (ID do aplicativo URI para a API da Web).
1. O código de autorização e as informações sobre o aplicativo Web e a API Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. Em seguida, a API da Web valida o token JWT e, se a validação for bem-sucedida, retornará o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de usuário delegado com concessão de código de autorização OAuth 2,0

1. Um usuário já está conectado a um aplicativo Web, cujo mecanismo de autenticação é independente do Azure AD.
1. O aplicativo Web requer um código de autorização para adquirir um token de acesso, portanto, ele emite uma solicitação por meio do navegador para o ponto de extremidade de autorização do AD do Azure, fornecendo a ID do aplicativo e o URI de redirecionamento para o aplicativo Web após a autenticação bem-sucedida. O usuário entra no Azure AD.
1. Se o usuário do aplicativo Web ainda não tiver consentido que permita que o aplicativo Web chame a API da Web em seu nome, o usuário precisará consentir. O aplicativo exibirá as permissões necessárias e, se alguma delas for de nível de administrador, um usuário normal no diretório não poderá consentir. Esse consentimento se aplica a um aplicativo único e multilocatário. No caso de locatário único, um administrador pode executar o consentimento do administrador para dar consentimento em nome de seus usuários. Isso pode ser feito usando o botão `Grant Permissions` na [portal do Azure](https://portal.azure.com). 
1. Depois que o usuário tiver consentido, o aplicativo Web receberá o código de autorização de que precisa para adquirir um token de acesso.
1. Usando o código de autorização emitido pelo Azure AD, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (ID do aplicativo URI para a API da Web).
1. O código de autorização e as informações sobre o aplicativo Web e a API Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. Em seguida, a API da Web valida o token JWT e, se a validação for bem-sucedida, retornará o recurso desejado.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para cenários de aplicativo Web para API da Web. E verifique com frequência--novos exemplos são adicionados com frequência. [Aplicativo Web para API da Web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registo da aplicação

Para registrar um aplicativo com o ponto de extremidade v 1.0 do Azure AD, consulte [registrar um aplicativo](quickstart-register-app.md).

* Locatário único – para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo Web e a API Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo Web a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo Web precisará selecionar as permissões desejadas no menu suspenso **permissões para outros aplicativos** na portal do Azure. Esta etapa não será necessária se o tipo de identidade do aplicativo estiver sendo usado.
* Multilocatário-primeiro, o aplicativo Web é configurado para indicar as permissões que ele requer para ser funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento ao aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões em nível de usuário, às quais qualquer usuário na organização pode consentir. Outros aplicativos exigem permissões de nível de administrador, às quais um usuário da organização não pode consentir. Somente um administrador de diretório pode dar consentimento a aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador consentir, o aplicativo Web e a API da Web são registrados em seu diretório.

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo Web usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem exigir que eles entrem novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
