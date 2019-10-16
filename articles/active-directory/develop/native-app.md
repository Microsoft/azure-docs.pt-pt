---
title: Aplicativos nativos no Azure Active Directory
description: Descreve o que são aplicativos nativos e as noções básicas sobre o fluxo de protocolo, o registro e a expiração do token para esse tipo de aplicativo.
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
ms.openlocfilehash: 0034668231d97e64602bdbdd0836bded97bb733d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373878"
---
# <a name="native-apps"></a>Aplicativos nativos

Aplicativos nativos são aplicativos que chamam uma API da Web em nome de um usuário. Esse cenário se baseia no tipo de concessão de código de autorização OAuth 2,0 com um cliente público, conforme descrito na seção 4,1 da [especificação OAuth 2,0](https://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso para o usuário usando o protocolo OAuth 2,0. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo nativo para API da Web](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

Se você estiver usando as bibliotecas de autenticação do AD, a maioria dos detalhes de protocolo descritos abaixo será tratada para você, como o pop-up do navegador, o cache de token e a manipulação de tokens de atualização.

1. Usando um pop-up de navegador, o aplicativo nativo faz uma solicitação para o ponto de extremidade de autorização no Azure AD. Essa solicitação inclui a ID do aplicativo e o URI de redirecionamento do aplicativo nativo, conforme mostrado na portal do Azure e o URI da ID do aplicativo para a API da Web. Se o usuário ainda não tiver entrado, será solicitado que ele entre novamente
1. O Azure AD autentica o usuário. Se for um aplicativo multilocatário e o consentimento for necessário para usar o aplicativo, o usuário será solicitado a consentir se ele ainda não tiver feito isso. Depois de conceder consentimento e após a autenticação bem-sucedida, o Azure AD emite uma resposta de código de autorização de volta para o URI de redirecionamento do aplicativo cliente.
1. Quando o Azure AD emite uma resposta de código de autorização de volta para o URI de redirecionamento, o aplicativo cliente interrompe a interação com o navegador e extrai o código de autorização da resposta. Usando esse código de autorização, o aplicativo cliente envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para o API da Web).
1. O código de autorização e as informações sobre o aplicativo cliente e a API Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD retorna informações básicas sobre o usuário, como seu nome de exibição e a ID do locatário.
1. Via HTTPS, o aplicativo cliente usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. Em seguida, a API da Web valida o token JWT e, se a validação for bem-sucedida, retornará o recurso desejado.
1. Quando o token de acesso expira, o aplicativo cliente recebe um erro que indica que o usuário precisa se autenticar novamente. Se o aplicativo tiver um token de atualização válido, ele poderá ser usado para adquirir um novo token de acesso sem solicitar que o usuário entre novamente. Se o token de atualização expirar, o aplicativo precisará autenticar o usuário interativamente mais uma vez.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser usado para acessar vários recursos. Por exemplo, se você tiver um aplicativo cliente que tenha permissão para chamar duas APIs Web, o token de atualização poderá ser usado para obter um token de acesso para a outra API da Web também.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para cenários de aplicativo nativo para API da Web. E volte a verificar com frequência--adicionamos novos exemplos com frequência. [Aplicativo nativo para API da Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registo da aplicação

Para registrar um aplicativo com o ponto de extremidade v 1.0 do Azure AD, consulte [registrar um aplicativo](quickstart-register-app.md).

* Locatário único-tanto o aplicativo nativo quanto a API Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo nativo a seus recursos. Em seguida, o aplicativo cliente seleciona as permissões desejadas no menu suspenso "permissões para outros aplicativos" no portal do Azure.
* Multilocatário-primeiro, o aplicativo nativo já se registrou apenas no diretório do desenvolvedor ou do editor. Em segundo lugar, o aplicativo nativo é configurado para indicar as permissões que ele exige para ser funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento ao aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões em nível de usuário, às quais qualquer usuário na organização pode consentir. Outros aplicativos exigem permissões de nível de administrador, às quais um usuário da organização não pode consentir. Somente um administrador de diretório pode dar consentimento a aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador consentir, somente a API da Web será registrada em seu diretório. 

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo nativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem exigir que eles entrem novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [tipos de aplicativos e cenários](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
