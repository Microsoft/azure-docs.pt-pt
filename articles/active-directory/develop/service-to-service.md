---
title: Aplicações de serviço para serviço no Azure Active Directory
description: Descreve quais aplicativos de serviços e as noções básicas sobre fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
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
ms.openlocfilehash: 683664b3172cb12ba6adf6c8006e9685a6d1ec35
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540303"
---
# <a name="service-to-service-apps"></a>Aplicações de serviço a serviço

Os aplicativos de serviço para serviço podem ser um aplicativo de daemon ou servidor que precisa para obter recursos de uma API web. Existem dois cenários secundárias que se aplicam a esta secção:

- Um daemon que precisa para chamar uma API web, baseada no tipo de concessão de credenciais de cliente OAuth 2.0

    Neste cenário, é importante compreender algumas coisas. Em primeiro lugar, a interação do utilizador não é possível com uma aplicação de daemon, que requer que a aplicação ter sua própria identidade. Um exemplo de um aplicativo de daemon é uma tarefa do batch, ou um serviço de sistema operativo em execução em segundo plano. Este tipo de aplicativo solicita um token de acesso ao utilizar a sua identidade da aplicação e apresentar o seu ID da aplicação, a credencial (palavra-passe ou certificado) e o aplicativo URI de ID para o Azure AD. Após a autenticação com êxito, o daemon de recebe um token de acesso do Azure AD, em seguida, é utilizado para chamar a API web.

- Um aplicativo de servidor (por exemplo, uma API web) que precisa chamar uma API web, criado na especificação do OAuth 2.0 On-Behalf-Of rascunho

    Neste cenário, imagine que um utilizador foi autenticado num aplicativo nativo, e esse aplicativo nativo precisa para chamar uma API web. O Azure AD emite um token de acesso do JWT para chamar a API web. Se a API web precisa chamar outra API web a jusante, ele pode usar o fluxo em-nome-de delegar a identidade do utilizador e autenticar para a API da web de segundo nível.

## <a name="diagram"></a>Diagrama

![O daemon ou uma aplicação de servidor para o diagrama da Web API](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>Fluxo de DProtocol

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder a identidade da aplicação com credenciais de cliente OAuth 2.0

1. Em primeiro lugar, a aplicação de servidor tem de autenticar com o Azure AD em nome próprio, sem qualquer interação humana, como um diálogo de início de sessão interativo. Ele faz um pedido ao ponto final de token do Azure AD, fornecendo a credencial, o ID da aplicação e o URI de ID da aplicação.
1. O Azure AD autentica o aplicativo e devolve um token de acesso do JWT que é utilizado para chamar a API web.
1. Através de HTTPS, a aplicação web utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem-sucedida, retorna o recurso pretendido.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de utilizador delegado com a especificação do OAuth 2.0 On-Behalf-Of rascunho

O fluxo abordado a seguir pressupõe que um utilizador ser autenticado no outro aplicativo (como uma aplicação nativa) e a identidade de utilizador foi utilizada para adquirir um token de acesso para a API da web de primeiro nível.

1. O aplicativo nativo envia o token de acesso para a API da web de primeiro nível.
1. A API da web de primeiro nível envia um pedido ao ponto final de token do Azure AD, fornecendo o ID da aplicação e as credenciais, bem como o token de acesso do utilizador. Além disso, a solicitação é enviada com um on_behalf_of parâmetro que indica a web API é solicitar novos tokens para chamar uma API web a jusante em nome do utilizador original.
1. O Azure AD verifica que a API da web de primeiro nível tem permissões para aceder a API da web de segundo nível e valida a solicitação, retornando um token de acesso do JWT e token para a API da web de primeiro nível de atualização de um JWT.
1. Através de HTTPS, a API web de primeiro nível, em seguida, chama a API da web de segundo camada, acrescentando a cadeia de caracteres de token no cabeçalho de autorização no pedido. A API da web de primeiro nível pode continuar a chamar a API da web de segundo nível, desde que o token de acesso e tokens de atualização são válidos.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código para Daemon ou uma aplicação de servidor para cenários de Web API. E, novamente, com frequência, como novos exemplos são adicionados com frequência de verificação. [Servidor ou aplicativo de Daemon, a API Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registo de aplicações

* Único inquilino - para a identidade da aplicação e o utilizador delegado casos de identidade, o daemon ou aplicação de servidor têm de estar registrada no mesmo diretório no Azure AD. A API web pode ser configurada para expor um conjunto de permissões, que são utilizados para limitar o daemon ou acesso ao seus recursos do servidor. Se está a ser utilizado um tipo de identidade do utilizador delegado, a aplicação de servidor tem de selecionar as permissões pretendidas no menu pendente "Permissões para outras aplicações" no portal do Azure. Este passo não é necessário se o tipo de identidade da aplicação está a ser utilizado.
* Multi-inquilino-First, a aplicação de daemon ou o servidor está configurado para indicar as permissões que ele requer que seja funcional. Esta lista de permissões necessárias é mostrada numa caixa de diálogo quando um utilizador ou administrador no diretório de destino dá consentimento para a aplicação, o que torna disponível para a sua organização. Apenas algumas aplicações necessitam de permissões de nível de usuário, que pode consentir qualquer utilizador na organização. Outras aplicações requerem permissões de nível de administrador, que não pode autorizar um utilizador na organização. Apenas um administrador do diretório pode dar consentimento a aplicações que exigem esse nível de permissões. Quando o utilizador ou administrador autorizar, ambas as APIs da web estão registadas no respetivo diretório.

## <a name="token-expiration"></a>Expiração do token

Quando o primeiro aplicativo utiliza o seu código de autorização ao obter o token de acesso de um JWT, também recebe um token de atualização do JWT. Quando o token de acesso expira, o token de atualização pode servir-se novamente à autenticação do utilizador sem pedir credenciais. Este token de atualização, em seguida, é utilizado para autenticar o utilizador, que resulta num novo token de acesso e o token de atualização.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre outras [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre o Azure AD [Noções básicas de autenticação](authentication-scenarios.md)
