---
title: Autenticação e autorização – serviço de Azure App | Microsoft Docs
description: Referência conceitual e visão geral do recurso de autenticação/autorização para o serviço Azure App
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: e308b44fffff451daa92cbf19209a1bcbfd4bff6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087987"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure

> [!NOTE]
> Neste momento, o AAD v2 (incluindo MSAL) não tem suporte para serviços Azure App e Azure Functions. Verifique novamente se há atualizações.
>

O serviço de Azure App fornece suporte interno de autenticação e autorização, para que você possa conectar usuários e acessar dados escrevendo um mínimo ou nenhum código em seu aplicativo Web, API RESTful e back-end móvel e também [Azure Functions](../azure-functions/functions-overview.md). Este artigo descreve como o serviço de aplicativo ajuda a simplificar a autenticação e a autorização para seu aplicativo. 

A autenticação e a autorização seguras exigem uma profunda compreensão da segurança, incluindo Federação, criptografia, gerenciamento [JWT (token Web JSON)](https://wikipedia.org/wiki/JSON_Web_Token) , [tipos de concessão](https://oauth.net/2/grant-types/)e assim por diante. O serviço de aplicativo fornece esses utilitários para que você possa passar mais tempo e energia para fornecer valor comercial ao cliente.

> [!NOTE]
> Não é necessário usar o serviço de aplicativo para autenticação e autorização. Muitas estruturas da Web são agrupadas com recursos de segurança e você pode usá-las, se desejar. Se precisar de mais flexibilidade do que o serviço de aplicativo fornece, você também poderá escrever seus próprios utilitários.  
>

Para obter informações específicas sobre aplicativos móveis nativos, consulte [autenticação de usuário e autorização para aplicativos móveis com o serviço de Azure app](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Como funciona

O módulo de autenticação e autorização é executado na mesma área restrita que o código do aplicativo. Quando habilitada, cada solicitação HTTP de entrada passa por ela antes de ser manipulada pelo código do aplicativo.

![](media/app-service-authentication-overview/architecture.png)

Este módulo lida com várias coisas para seu aplicativo:

- Autentica usuários com o provedor especificado
- Valida, armazena e atualiza tokens
- Gerencia a sessão autenticada
- Injeta informações de identidade em cabeçalhos de solicitação

O módulo é executado separadamente do código do aplicativo e é configurado usando as configurações do aplicativo. Não são necessários SDKs, idiomas específicos ou alterações no código do aplicativo. 

### <a name="user-claims"></a>Afirmações do utilizador

Para todas as estruturas de linguagem, o serviço de aplicativo disponibiliza as declarações do usuário para seu código injetando-as nos cabeçalhos de solicitação. Para aplicativos ASP.NET 4,6, o serviço de aplicativo popula [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as declarações do usuário autenticado, para que você possa seguir o padrão de código .NET padrão `[Authorize]` , incluindo o atributo. Da mesma forma, para aplicativos PHP, o serviço de `_SERVER['REMOTE_USER']` aplicativo popula a variável. Para aplicativos Java, as declarações podem [ser acessadas do servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Por [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` não é alimentado pelo código .net, mas você ainda pode encontrar as declarações do usuário nos cabeçalhos da solicitação.

Para obter mais informações, consulte [acessar declarações de usuário](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Repositório de token

O serviço de aplicativo fornece um repositório de token interno, que é um repositório de tokens associados aos usuários de seus aplicativos Web, APIs ou aplicativos móveis nativos. Quando você habilita a autenticação com qualquer provedor, esse repositório de tokens fica imediatamente disponível para seu aplicativo. Se o código do aplicativo precisar acessar dados desses provedores em nome do usuário, como: 

- postar na linha do tempo do Facebook do usuário autenticado
- ler os dados corporativos do usuário do Azure Active Directory API do Graph ou até mesmo o Microsoft Graph

Normalmente, você deve escrever código para coletar, armazenar e atualizar esses tokens em seu aplicativo. Com o token Store, você apenas [recupera os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisa deles e [informa ao serviço de aplicativo para atualizá-los](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando eles se tornarem inválidos. 

Os tokens de ID, tokens de acesso e tokens de atualização armazenados em cache para a sessão autenticada, e eles são acessíveis somente pelo usuário associado.  

Se você não precisar trabalhar com tokens em seu aplicativo, poderá desabilitar o token Store.

### <a name="logging-and-tracing"></a>Registro em log e rastreamento

Se você [habilitar o log do aplicativo](troubleshoot-diagnostic-logs.md), verá rastreamentos de autenticação e autorização diretamente em seus arquivos de log. Se você vir um erro de autenticação que não esperava, poderá encontrar convenientemente todos os detalhes examinando os logs de aplicativo existentes. Se você habilitar o [rastreamento de solicitação com falha](troubleshoot-diagnostic-logs.md), poderá ver exatamente qual função o módulo de autenticação e autorização pode ter reproduzido em uma solicitação com falha. Nos logs de rastreamento, Procure referências a um módulo chamado `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Fornecedores de identidade

O serviço de aplicativo usa a [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), na qual um provedor de identidade de terceiros gerencia as identidades de usuário e o fluxo de autenticação para você. Cinco provedores de identidade estão disponíveis por padrão: 

| Fornecedor | Ponto de extremidade de entrada |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Conta Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Quando você habilita a autenticação e a autorização com um desses provedores, seu ponto de extremidade de entrada está disponível para autenticação de usuário e para validação de tokens de autenticação do provedor. Você pode fornecer a seus usuários qualquer número dessas opções de conexão com facilidade. Você também pode integrar outro provedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

## <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os provedores, mas difere dependendo se você deseja entrar com o SDK do provedor:

- Sem o SDK do provedor: O aplicativo delega a entrada federada ao serviço de aplicativo. Normalmente, esse é o caso de aplicativos de navegador, que podem apresentar a página de logon do provedor ao usuário. O código do servidor gerencia o processo de entrada, portanto, ele também é chamado de fluxo direcionado ao _servidor_ ou _fluxo do servidor_. Esse caso se aplica a aplicativos de navegador. Ele também se aplica a aplicativos nativos que conectam usuários usando o SDK do cliente de aplicativos móveis porque o SDK abre uma exibição da Web para conectar usuários com a autenticação do serviço de aplicativo. 
- Com o SDK do provedor: O aplicativo conecta os usuários ao provedor manualmente e, em seguida, envia o token de autenticação para o serviço de aplicativo para validação. Normalmente, esse é o caso com aplicativos sem navegador, que não podem apresentar a página de entrada do provedor ao usuário. O código do aplicativo gerencia o processo de entrada, portanto, ele também é chamado de fluxo direcionado ao _cliente_ ou _fluxo do cliente_. Esse caso se aplica a APIs REST, [Azure Functions](../azure-functions/functions-overview.md)e clientes de navegador JavaScript, bem como aplicativos de navegador que precisam de mais flexibilidade no processo de entrada. Ele também se aplica a aplicativos móveis nativos que assinam usuários usando o SDK do provedor.

> [!NOTE]
> Chamadas de um aplicativo de navegador confiável no serviço de aplicativo chamam outra API REST no serviço de aplicativo ou [Azure Functions](../azure-functions/functions-overview.md) podem ser autenticadas usando o fluxo direcionado ao servidor. Para obter mais informações, consulte [Personalizar a autenticação e a autorização no serviço de aplicativo](app-service-authentication-how-to.md).
>

A tabela a seguir mostra as etapas do fluxo de autenticação.

| Passo | Sem o SDK do provedor | Com o SDK do provedor |
| - | - | - |
| 1. Conectar usuário | Redireciona o cliente para `/.auth/login/<provider>`. | O código do cliente assina o usuário diretamente com o SDK do provedor e recebe um token de autenticação. Para obter informações, consulte a documentação do provedor. |
| 2. Pós-autenticação | O provedor redireciona o cliente para `/.auth/login/<provider>/callback`o. | O código [do cliente posta o token do provedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` para validação. |
| 3. Estabelecer sessão autenticada | O serviço de aplicativo adiciona o cookie autenticado à resposta. | O serviço de aplicativo retorna seu próprio token de autenticação para o código do cliente. |
| 4. Fornecer conteúdo autenticado | O cliente inclui o cookie de autenticação em solicitações subsequentes (manipuladas automaticamente pelo navegador). | O código do cliente apresenta o `X-ZUMO-AUTH` token de autenticação no cabeçalho (manipulado automaticamente pelos SDKs do cliente de aplicativos móveis). |

Para navegadores de cliente, o serviço de aplicativo pode direcionar automaticamente todos `/.auth/login/<provider>`os usuários não autenticados para o. Você também pode apresentar aos usuários um ou mais `/.auth/login/<provider>` links para entrar em seu aplicativo usando seu provedor de preferência.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento de autorização

Na [portal do Azure](https://portal.azure.com), você pode configurar a autorização do serviço de aplicativo com vários comportamentos quando a solicitação de entrada não for autenticada.

![](media/app-service-authentication-overview/authorization-flow.png)

Os títulos a seguir descrevem as opções.

### <a name="allow-anonymous-requests-no-action"></a>Permitir solicitações anônimas (nenhuma ação)

Essa opção adia a autorização de tráfego não autenticado para o código do aplicativo. Para solicitações autenticadas, o serviço de aplicativo também passa pelas informações de autenticação nos cabeçalhos HTTP. 

Essa opção fornece mais flexibilidade no tratamento de solicitações anônimas. Por exemplo, ele permite [apresentar vários provedores de entrada](app-service-authentication-how-to.md#use-multiple-sign-in-providers) para seus usuários. No entanto, você deve escrever o código. 

### <a name="allow-only-authenticated-requests"></a>Permitir somente solicitações autenticadas

A opção é **fazer logon com \<o provedor >** . O serviço de aplicativo redireciona todas as solicitações anônimas para `/.auth/login/<provider>` o provedor que você escolher. Se a solicitação anônima vier de um aplicativo móvel nativo, a resposta retornada será um `HTTP 401 Unauthorized`.

Com essa opção, você não precisa escrever nenhum código de autenticação em seu aplicativo. Uma autorização mais refinada, como autorização específica de função, pode ser manipulada inspecionando as declarações do usuário (consulte [acessar declarações de usuário](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única.

## <a name="more-resources"></a>Mais recursos

[Tutorial: Autenticar e autorizar usuários de ponta a ponta no serviço de Azure App (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticar e autorizar usuários de ponta a ponta no serviço de Azure App para Linux](containers/tutorial-auth-aad.md)  
[Personalizar a autenticação e a autorização no serviço de aplicativo](app-service-authentication-how-to.md)

Guias de instruções específicas do provedor:

* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory][AAD]
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook][Facebook]
* [Como configurar a sua aplicação para utilizar o início de sessão do Google][Google]
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft][MSA]
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter][Twitter]
* [How to: Usar a autenticação personalizada para seu aplicativo][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
