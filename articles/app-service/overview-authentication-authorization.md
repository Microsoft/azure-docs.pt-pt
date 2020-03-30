---
title: Autenticação e autorização
description: Saiba mais sobre a autenticação incorporada e suporte de autorização no Serviço de Aplicações Azure, e como pode ajudar a proteger a sua aplicação contra acesso não autorizado.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 825d113bbe081ba6fb85da19ff6449824db92d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475396"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure

> [!NOTE]
> Neste momento, o AAD V2 (incluindo o MSAL) não é suportado para serviços de aplicações azure e funções azure. Por favor, verifique se há novidades.
>

O Azure App Service fornece suporte de autenticação e autorização incorporado, para que possa inscrever os utilizadores e aceder aos dados escrevendo o mínimo ou nenhum código na sua aplicação web, a API RESTful e a traseira móvel, bem como as [Funções Azure.](../azure-functions/functions-overview.md) Este artigo descreve como o Serviço de Aplicações ajuda a simplificar a autenticação e a autorização para a sua aplicação.

A autenticação e a autorização seguras requerem uma compreensão profunda da segurança, incluindo a federação, encriptação, gestão de [tokens web JSON (JWT),](https://wikipedia.org/wiki/JSON_Web_Token) tipos de [subvenções,](https://oauth.net/2/grant-types/)e assim por diante. O Serviço de Aplicações fornece estes utilitários para que possa gastar mais tempo e energia em fornecer valor de negócio ao seu cliente.

> [!IMPORTANT]
> Não é obrigado a utilizar o Serviço de Aplicações para AuthN/AuthO. Pode utilizar as funcionalidades de segurança agregadas no seu quadro web de eleição, ou pode escrever os seus próprios utilitários. No entanto, tenha em mente que o [Chrome 80 está a fazer alterações na sua implementação do SameSite para cookies](https://www.chromestatus.com/feature/5088147346030592) (data de lançamento em março de 2020), e a autenticação remota personalizada ou outros cenários que dependem da publicação de cookies transsite podem quebrar quando os navegadores Chrome do cliente forem atualizados. A suticidade é complexa porque precisa de suportar diferentes comportamentos do SameSite para diferentes navegadores. 
>
> As versões ASP.NET Core 2.1 e acima hospedadas pelo App Service já estão corrigidas para esta alteração de rutura e manuseiem adequadamente o Chrome 80 e navegadores mais antigos. Além disso, o mesmo patch para ASP.NET Quadro 4.7.2 está a ser implementado nos casos do Serviço de Aplicações ao longo de janeiro de 2020. Para mais informações, incluindo como saber se a sua aplicação recebeu o patch, consulte a atualização de [cookies do Mesmo Site do Serviço de Aplicações Azure](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Para obter informações específicas para aplicações móveis nativas, consulte a [autenticação do Utilizador e a autorização para aplicações móveis com o Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Como funciona

O módulo de autenticação e autorização funciona na mesma caixa de areia que o seu código de aplicação. Quando está ativado, todos os pedidos http que chegam passam por ele antes de serem tratados pelo seu código de aplicação.

![](media/app-service-authentication-overview/architecture.png)

Este módulo lida com várias coisas para a sua aplicação:

- Autentica os utilizadores com o fornecedor especificado
- Valida, lojas e fichas refrescantes
- Gere a sessão autenticada
- Injeta informações de identidade em cabeçalhos de pedido

O módulo funciona separadamente do seu código de aplicação e é configurado utilizando as definições da aplicação. Não são necessários SDKs, línguas específicas ou alterações ao código de aplicação. 

### <a name="user-claims"></a>Reclamações dos utilizadores

Para todos os quadros linguísticos, o Serviço de Aplicações disponibiliza as reclamações do utilizador ao seu código injetando-as nos cabeçalhos de pedido. Para ASP.NET 4.6 aplicações, o Serviço de Aplicações povoa [o ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as alegações do `[Authorize]` utilizador autenticado, para que possa seguir o padrão de código .NET padrão, incluindo o atributo. Da mesma forma, para aplicações PHP, o App Service povoa a `_SERVER['REMOTE_USER']` variável. Para aplicações Java, as reclamações são [acessíveis a partir do servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Para [funções Azure,](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` não é hidratado para o código .NET, mas ainda pode encontrar as reclamações do utilizador nos cabeçalhos de pedido.

Para mais informações, consulte [as reclamações dos utilizadores](app-service-authentication-how-to.md#access-user-claims)do Access.

### <a name="token-store"></a>Arquivo de tokens

O App Service fornece uma loja de token incorporada, que é um repositório de fichas que estão associadas aos utilizadores das suas aplicações web, APIs ou aplicações móveis nativas. Quando ativa a autenticação com qualquer fornecedor, esta loja token está imediatamente disponível para a sua aplicação. Se o seu código de aplicação necessitar de aceder a dados destes fornecedores em nome do utilizador, tais como: 

- post para a linha temporal do Facebook do utilizador autenticado
- ler os dados corporativos do utilizador usando a API do Microsoft Graph

Normalmente, tem de escrever código para recolher, armazenar e refrescar estes tokens na sua aplicação. Com a loja de [fichas, basta recuperar os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar e dizer ao Serviço de [Aplicações para os refrescar](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando ficarem inválidos. 

Os tokens id, tokens de acesso e fichas de atualização em cache para a sessão autenticada, e eles são acessíveis apenas pelo utilizador associado.  

Se não precisar de trabalhar com fichas na sua aplicação, pode desativar a loja de fichas.

### <a name="logging-and-tracing"></a>Exploração madeireira e rastreio

Se [ativar](troubleshoot-diagnostic-logs.md)o registo de pedidos, verá a autenticação e a autorização rastreia-os diretamente nos seus ficheiros de registo. Se vir um erro de autenticação que não esperava, pode convenientemente encontrar todos os detalhes olhando para os registos de aplicação existentes. Se ativar o rastreio de [pedidos falhados,](troubleshoot-diagnostic-logs.md)pode ver exatamente qual o papel que o módulo de autenticação e autorização pode ter desempenhado num pedido falhado. Nos registos de rastreio, procure referências `EasyAuthModule_32/64`a um módulo chamado . 

## <a name="identity-providers"></a>Fornecedores de identidade

O Serviço de Aplicações utiliza [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), na qual um fornecedor de identidade de terceiros gere as identidades dos utilizadores e o fluxo de autenticação para si. Cinco fornecedores de identidade estão disponíveis por defeito: 

| Fornecedor | Ponto final de inscrição |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Conta Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Quando permite a autenticação e autorização com um destes fornecedores, o seu ponto final de sessão está disponível para autenticação do utilizador e para validação de fichas de autenticação do fornecedor. Pode fornecer aos seus utilizadores qualquer número destas opções de sessão com facilidade. Também pode integrar outro fornecedor de identidade ou [a sua própria solução de identidade personalizada.][custom-auth]

## <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os fornecedores, mas difere consoante pretenda iniciar sessão com o SDK do fornecedor:

- Sem o fornecedor SDK: Os delegados de aplicação federaram o serviço de aplicação. Este é normalmente o caso das aplicações do navegador, que podem apresentar a página de login do fornecedor ao utilizador. O código do servidor gere o processo de entrada, pelo que também é chamado de fluxo de servidor ou fluxo _de_ _servidor_. Este caso aplica-se às aplicações do navegador. Aplica-se também a aplicações nativas que assinam utilizadores na utilização do cliente de Aplicações Móveis SDK porque o SDK abre uma visão web para iniciar sessão com a autenticação do App Service. 
- Com o fornecedor SDK: A aplicação inscreve os utilizadores manualmente no fornecedor e, em seguida, envia o símbolo de autenticação ao Serviço de Aplicações para validação. Este é normalmente o caso de aplicações sem navegador, que não podem apresentar a página de entrada do fornecedor ao utilizador. O código de aplicação gere o processo de entrada, pelo que também é chamado fluxo _de clientes_ ou fluxo de _cliente_. Este caso aplica-se aos clientes REST APIs, [Azure Functions](../azure-functions/functions-overview.md)e JavaScript, bem como aplicações de navegador que precisam de mais flexibilidade no processo de entrada. Aplica-se também a aplicações móveis nativas que assinam os utilizadores na utilização do SDK do fornecedor.

> [!NOTE]
> As chamadas de uma aplicação de navegador fidedigna no Serviço de Aplicações chamam outra API REST no Serviço de Aplicações ou [funções Azure](../azure-functions/functions-overview.md) podem ser autenticadas usando o fluxo direcionado para o servidor. Para mais informações, consulte Personalizar a autenticação e autorização no Serviço de [Aplicações.](app-service-authentication-how-to.md)
>

A tabela abaixo mostra os passos do fluxo de autenticação.

| Passo | Sem provedor SDK | Com o provedor SDK |
| - | - | - |
| 1. Iniciar sessão no utilizador | Redireciona `/.auth/login/<provider>`o cliente para . | O código do cliente assina o utilizador diretamente com o SDK do fornecedor e recebe um símbolo de autenticação. Para obter informações, consulte a documentação do fornecedor. |
| 2. Pós-autenticação | O fornecedor redireciona o cliente para `/.auth/login/<provider>/callback`. | Os [posts de](app-service-authentication-how-to.md#validate-tokens-from-providers) código `/.auth/login/<provider>` do cliente são token do fornecedor para a validação. |
| 3. Estabelecer sessão autenticada | O Serviço de Aplicações adiciona cookie autenticado à resposta. | O Serviço de Aplicações devolve o seu próprio símbolo de autenticação ao código do cliente. |
| 4. Servir conteúdo autenticado | O cliente inclui cookie de autenticação em pedidos subsequentes (tratado automaticamente pelo navegador). | O código do cliente `X-ZUMO-AUTH` apresenta token de autenticação no cabeçalho (tratado automaticamente por SDKs clientes de Aplicações Móveis). |

Para os navegadores de clientes, o Serviço de `/.auth/login/<provider>`Aplicações pode direcionar automaticamente todos os utilizadores não autenticados para . Também pode apresentar aos utilizadores `/.auth/login/<provider>` uma ou mais links para iniciar sessão na sua aplicação utilizando o seu fornecedor de eleição.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento de autorização

No [portal Azure,](https://portal.azure.com)pode configurar a autorização do Serviço de Aplicações com vários comportamentos quando o pedido de entrada não é autenticado.

![](media/app-service-authentication-overview/authorization-flow.png)

Os seguintes títulos descrevem as opções.

### <a name="allow-anonymous-requests-no-action"></a>Permitir pedidos anónimos (sem ação)

Esta opção adia a autorização de tráfego não autenticado para o seu código de aplicação. Para pedidos autenticados, o Serviço de Aplicações também passa informações de autenticação nos cabeçalhos HTTP. 

Esta opção proporciona mais flexibilidade no tratamento de pedidos anónimos. Por exemplo, permite-lhe [apresentar vários fornecedores de sessão](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos seus utilizadores. No entanto, tem de escrever código. 

### <a name="allow-only-authenticated-requests"></a>Permitir apenas pedidos autenticados

A opção é **iniciar sessão com \<o fornecedor>**. O Serviço de Aplicações redireciona todos os pedidos anónimos para `/.auth/login/<provider>` o fornecedor que escolher. Se o pedido anónimo vier de uma aplicação `HTTP 401 Unauthorized`móvel nativa, a resposta devolvida é uma .

Com esta opção, não precisa de escrever qualquer código de autenticação na sua aplicação. A autorização mais fina, como a autorização específica para o papel, pode ser tratada inspecionando as reclamações do utilizador (ver [Access user claims](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que pretendam uma página inicial publicamente disponível, como em muitas aplicações de página única.

## <a name="more-resources"></a>Mais recursos

[Tutorial: Autenticar e autorizar utilizadores de ponta a ponta no Serviço de Aplicações Azure (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticar e autorizar utilizadores de ponta a ponta no Serviço de Aplicações Azure para linux](containers/tutorial-auth-aad.md)  
[Personalizar a autenticação e autorização no Serviço de Aplicações](app-service-authentication-how-to.md)

Guias específicos do fornecedor:

* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory][AAD]
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook][Facebook]
* [Como configurar a sua aplicação para utilizar o início de sessão do Google][Google]
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft][MSA]
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter][Twitter]
* [Como: Utilizar a autenticação personalizada para a sua aplicação][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
