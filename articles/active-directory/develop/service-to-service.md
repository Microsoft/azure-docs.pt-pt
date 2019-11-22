---
title: Aplicativos de serviço a serviço no Azure Active Directory
description: Descreve quais aplicativos de serviço a serviço e os conceitos básicos sobre o fluxo de protocolo, o registro e a expiração do token para esse tipo de aplicativo.
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
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49081ba72559b021d2e4846e7d9feffd61ae7b36
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284904"
---
# <a name="service-to-service-apps"></a>Aplicativos de serviço a serviço

Aplicativos de serviço a serviço podem ser um aplicativo de daemon ou de servidor que precisa obter recursos de uma API da Web. Há dois subcenários que se aplicam a esta seção:

- Um daemon que precisa chamar uma API da Web, criada em tipo de concessão de credenciais de cliente OAuth 2,0

    Nesse cenário, é importante entender algumas coisas. Primeiro, a interação do usuário não é possível com um aplicativo daemon, o que exige que o aplicativo tenha sua própria identidade. Um exemplo de um aplicativo daemon é um trabalho em lotes ou um serviço do sistema operacional em execução em segundo plano. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID de aplicativo, credencial (senha ou certificado) e URI de ID do aplicativo para o Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso do Azure AD, que é usado para chamar a API da Web.

- Um aplicativo de servidor (como uma API Web) que precisa chamar uma API da Web, criada na especificação de rascunho em nome de OAuth 2,0

    Nesse cenário, imagine que um usuário se autenticou em um aplicativo nativo, e esse aplicativo nativo precisa chamar uma API da Web. O Azure AD emite um token de acesso JWT para chamar a API da Web. Se a API da Web precisar chamar outra API da Web downstream, ela poderá usar o fluxo em nome de para delegar a identidade do usuário e autenticar para a API da Web de segunda camada.

## <a name="diagram"></a>Diagrama

![Diagrama de daemon ou aplicativo de servidor para API da Web](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais de cliente OAuth 2,0

1. Primeiro, o aplicativo de servidor precisa se autenticar com o Azure AD como ele mesmo, sem qualquer interação humana, como uma caixa de diálogo de logon interativo. Ele faz uma solicitação para o ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do aplicativo e o URI da ID do aplicativo.
1. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. Em seguida, a API da Web valida o token JWT e, se a validação for bem-sucedida, retornará o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de usuário delegado com especificação de rascunho em nome de OAuth 2,0

O fluxo discutido abaixo pressupõe que um usuário tenha sido autenticado em outro aplicativo (como um aplicativo nativo) e que sua identidade de usuário tenha sido usada para adquirir um token de acesso para a API da Web de primeira camada.

1. O aplicativo nativo envia o token de acesso para a API da Web de primeira camada.
1. A API da Web de primeira camada envia uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo sua ID de aplicativo e suas credenciais, bem como o token de acesso do usuário. Além disso, a solicitação é enviada com um parâmetro on_behalf_of que indica que a API da Web está solicitando novos tokens para chamar uma API Web downstream em nome do usuário original.
1. O Azure AD verifica se a API da Web de primeira camada tem permissões para acessar a API da Web de segunda camada e valida a solicitação, retornando um token de acesso JWT e um token de atualização JWT para a API da Web de primeira camada.
1. Via HTTPS, a API da Web de primeira camada chama a API da Web de segunda camada acrescentando a cadeia de caracteres do token no cabeçalho de autorização na solicitação. A API da Web de primeira camada pode continuar a chamar a API da Web de segunda camada, contanto que o token de acesso e os tokens de atualização sejam válidos.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para o aplicativo daemon ou de servidor para cenários da API Web: [servidor ou aplicativo daemon para API da Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registo da aplicação

* Locatário único – para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo de daemon ou de servidor deve ser registrado no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do daemon ou do servidor a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo de servidor precisará selecionar as permissões desejadas. Na página **permissão de API** para o registro do aplicativo, depois de selecionar **Adicionar uma permissão** e escolher a família de API, escolha **permissões delegadas**e, em seguida, selecione suas permissões. Esta etapa não será necessária se o tipo de identidade do aplicativo estiver sendo usado.
* Multilocatário-primeiro, o daemon ou aplicativo de servidor é configurado para indicar as permissões que ele requer para ser funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento ao aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões em nível de usuário, às quais qualquer usuário na organização pode consentir. Outros aplicativos exigem permissões de nível de administrador, às quais um usuário da organização não pode consentir. Somente um administrador de diretório pode dar consentimento a aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador consentir, ambas as APIs da Web serão registradas em seu diretório.

## <a name="token-expiration"></a>Expiração do token

Quando o primeiro aplicativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem solicitar credenciais. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
