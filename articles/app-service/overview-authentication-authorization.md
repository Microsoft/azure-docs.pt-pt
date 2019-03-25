---
title: Autenticação e autorização - serviço de aplicações do Azure | Documentos da Microsoft
description: Referência conceptual e descrição geral da autenticação / autorização de recursos para o serviço de aplicações do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: mahender,cephalin
ms.custom: seodec18
ms.openlocfilehash: d914e3ad3043b2671e154d1616c6800f34415c11
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402750"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure

> [!NOTE]
> Neste momento, o AAD V2 (incluindo MSAL) não é suportada para serviços de aplicações do Azure e as funções do Azure. Verifique novamente a existência de atualizações.
>

Serviço de aplicações do Azure fornece a autenticação e autorização incorporadas suportam, para que possa iniciar sessão dos utilizadores e aceder a dados ao escrever o mínimo ou nenhum código na sua aplicação web, uma RESTful API e um back-end móvel e também [as funções do Azure](../azure-functions/functions-overview.md). Este artigo descreve como o serviço de aplicações ajuda a simplificar a autenticação e autorização para a sua aplicação. 

Autorização e autenticação segura requerem uma compreensão profunda de segurança, incluindo o Federação, encriptação, [tokens de web JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) gerenciamento [tipos de concessão](https://oauth.net/2/grant-types/)e assim por diante. Serviço de aplicações fornece estes utilitários, para que possa passar mais tempo e energia em fornecer valor comercial para o seu cliente.

> [!NOTE]
> Não é necessária para utilizar o serviço de aplicações para autenticação e autorização. Muitas estruturas de web são agrupadas com recursos de segurança, e pode usá-los se assim o desejar. Se precisar de mais flexibilidade do serviço de aplicações fornece, também pode escrever seus próprios utilitários.  
>

Para obter informações específicas de aplicações móveis nativas, veja [autenticação de utilizador e a autorização para aplicações móveis com o serviço de aplicações do Azure](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Como funciona

O módulo de autenticação e autorização é executado na sandbox do mesmo como o código da aplicação. Quando estiver ativada, todos os pedidos HTTP de entrada passam através do mesmo antes de a ser processadas pelo código da aplicação.

![](media/app-service-authentication-overview/architecture.png)

Este módulo lida com várias coisas para a sua aplicação:

- Autentique os utilizadores com o fornecedor especificado
- Valida, armazena e atualiza os tokens
- Gere a sessão autenticada
- Injeta informações de identidade em cabeçalhos de pedido

O módulo é executado separadamente do código da aplicação e está configurado com as definições de aplicação. Não existem SDKs, linguagens específicas de ou alterações ao seu código de aplicativo são necessárias. 

### <a name="user-claims"></a>Afirmações do utilizador

Para todas as arquiteturas de linguagem, serviço de aplicações disponibiliza afirmações do utilizador para o seu código injetando-los para os cabeçalhos de pedido. Para aplicações ASP.NET 4.6, o serviço de aplicações preenche [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com afirmações do utilizador autenticado, pelo que pode seguir o padrão de código do .NET, incluindo o `[Authorize]` atributo. Da mesma forma, para aplicações do PHP, o serviço de aplicações preenche o `_SERVER['REMOTE_USER']` variável.

Para [as funções do Azure](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` não é alimentado para o código do .NET, mas ainda é possível encontrar as afirmações de utilizador nos cabeçalhos do pedido.

Para obter mais informações, consulte [afirmações de utilizador de acesso](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Arquivo de tokens

Serviço de aplicações fornece um arquivo de tokens incorporado, que é um repositório de tokens que estão associados com os utilizadores das suas aplicações web, APIs ou aplicações móveis nativas. Ao ativar a autenticação com qualquer fornecedor, este arquivo de tokens está imediatamente disponível à sua aplicação. Se o código da aplicação tem de aceder a dados desses provedores em nome do utilizador, tais como: 

- publicar na cronologia do Facebook do usuário autenticado
- ler os dados empresariais do utilizador a partir da API do Azure Active Directory Graph ou até mesmo o Microsoft Graph

Normalmente, deve escrever código para recolher, armazenar e atualizar esses tokens em seu aplicativo. Com o arquivo de tokens, acabou [recuperar os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar delas e [dizer ao serviço de aplicações para atualizá-los](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando eles se torne inválidos. 

Os tokens de id, tokens de acesso e tokens de atualização em cache para a sessão autenticada e estão acessíveis apenas pelo utilizador associado.  

Se não precisa de trabalhar com tokens na sua aplicação, pode desativar o arquivo de tokens.

### <a name="logging-and-tracing"></a>Registo e rastreio

Se [ativar o registo de aplicação](troubleshoot-diagnostic-logs.md), verá os rastreios de autenticação e autorização diretamente nos seus ficheiros de registo. Se vir um erro de autenticação que não pretende, pode encontrar todos os detalhes convenientemente através da consulta os registos da aplicação existente. Se habilitar [de pedidos falhados](troubleshoot-diagnostic-logs.md), pode ver exatamente que função a autenticação e o módulo de autorização pode ter tocado numa solicitação com falha. Nos logs de rastreamento, procurar referências a um módulo com o nome `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Fornecedores de identidade

Serviço de aplicações utiliza [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), em que um fornecedor de identidade de terceiros gere as identidades de utilizador e o fluxo de autenticação para. Cinco fornecedores de identidade estão disponíveis por predefinição: 

| Fornecedor | Ponto final de início de sessão |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Conta Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Quando ativa a autenticação e autorização com um destes fornecedores, o seu ponto de extremidade início de sessão está disponível para a autenticação de utilizador e para a validação de tokens de autenticação do fornecedor. Pode fornecer os seus utilizadores com qualquer número destas opções de início de sessão com facilidade. Também é possível integrar a outros fornecedores de identidade ou [sua própria solução de identidade personalizada][custom-auth].

## <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os fornecedores, mas diferente dependendo se pretende iniciar sessão com SDK o fornecedor:

- Sem o fornecedor de SDK: Delega o aplicativo federada início de sessão no serviço de aplicações. Isso normalmente é o caso com as aplicações de browser, que pode apresentar a página de início de sessão do fornecedor ao usuário. O código de servidor gerencia o processo de início de sessão, para que ele também é chamado _direcionado de servidor de fluxo_ ou _fluxo de servidor_. Este caso aplica-se às aplicações de browser. Ele também se aplica a aplicações nativas que inscrevem os utilizadores em sessão com o SDK do cliente de Mobile Apps, uma vez que o SDK abre uma vista web para assinar os utilizadores em autenticação do serviço de aplicações. 
- Com o fornecedor de SDK: O aplicativo inicia manualmente a utilizadores para o fornecedor e, em seguida, envia o token de autenticação no serviço de aplicações para a validação. Isso normalmente é o caso com aplicações sem browser que não é possível apresentar a página de início de sessão do fornecedor ao usuário. O código do aplicativo gerencia o processo de início de sessão, para que ele também é chamado _fluxo de cliente direcionado_ ou _fluxo de cliente_. Este caso aplica-se às APIs de REST [as funções do Azure](../azure-functions/functions-overview.md)e os clientes de browser de JavaScript, bem como aplicações de browser que precisam de mais flexibilidade no processo de início de sessão. Ele também se aplica a aplicações móveis nativas que inscrevem os utilizadores com o SDK do fornecedor.

> [!NOTE]
> Chamadas a partir de uma aplicação de browser confiável no serviço de aplicações chama outra API de REST no serviço de aplicações ou [as funções do Azure](../azure-functions/functions-overview.md) pode ser autenticado utilizando o fluxo de servidor direcionadas. Para obter mais informações, consulte [personalizar autenticação e autorização no serviço de aplicações](app-service-authentication-how-to.md).
>

A tabela abaixo mostra os passos para o fluxo de autenticação.

| Passo | Sem o fornecedor de SDK | Com o fornecedor de SDK |
| - | - | - |
| 1. Utilizador de início de sessão no | Redireciona o cliente para `/.auth/login/<provider>`. | Código de cliente inicia o utilizador sessão diretamente com o SDK do fornecedor e recebe um token de autenticação. Para obter informações, consulte a documentação do fornecedor. |
| 2. Pós-autenticação | Fornecedor redireciona o cliente para `/.auth/login/<provider>/callback`. | Código de cliente [publica o token de fornecedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` para validação. |
| 3. Estabelecer sessão autenticada | Serviço de aplicações adiciona cookie autenticado à resposta. | Serviço de aplicações retorna seu próprio token de autenticação para o código de cliente. |
| 4. Servir conteúdo autenticado | Cliente inclui o cookie de autenticação nas solicitações subseqüentes (manipuladas automaticamente pelas browser). | Código de cliente apresenta o token de autenticação no `X-ZUMO-AUTH` cabeçalho (manipulado automaticamente pelas SDKs de cliente de aplicações móveis). |

Para browsers do cliente, o serviço de aplicações podem encaminhar automaticamente todos os utilizadores não autenticados `/.auth/login/<provider>`. Também pode apresentar os utilizadores com uma ou mais `/.auth/login/<provider>` ligações para iniciar sessão na sua aplicação com o respetivo fornecedor de escolha.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento de autorização

Na [portal do Azure](https://portal.azure.com), pode configurar a autorização de serviço de aplicações com um número de comportamentos.

![](media/app-service-authentication-overview/authorization-flow.png)

Os cabeçalhos seguintes descrevem as opções.

### <a name="allow-all-requests-default"></a>Permitir que todos os pedidos (predefinição)

Autenticação e autorização, não são geridos pelo serviço de aplicações (desativado). 

Escolha esta opção se não precisar de autenticação e autorização, ou se quiser escrever seu próprio código de autenticação e autorização.

### <a name="allow-only-authenticated-requests"></a>Permitir apenas pedidos autenticados

É a opção **início de sessão \<fornecedor >**. Serviço de aplicações redireciona todas as solicitações anônimas `/.auth/login/<provider>` para o fornecedor que escolher. Se o pedido anónimo é proveniente de uma aplicação móvel nativa, a resposta retornada é um `HTTP 401 Unauthorized`.

Com esta opção, não terá de escrever qualquer código de autenticação na sua aplicação. Autorização mais, como autorização específicas de função, pode ser processada ao inspecionar as afirmações de utilizadores (consulte [afirmações de utilizador de acesso](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Permitir que todos os pedidos, mas validar pedidos autenticados

É a opção **solicita Permitir anónimo**. Esta opção ativa a autenticação e autorização no serviço de aplicações, mas difere decisões de autorização para o código da aplicação. Para pedidos autenticados, o serviço de aplicações também passa ao longo de informações de autenticação nos cabeçalhos de HTTP. 

Esta opção fornece mais flexibilidade na manipulação de pedidos anónimos. Por exemplo, permite-lhe [apresentam vários fornecedores de início de sessão](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos seus utilizadores. No entanto, deve escrever código. 

## <a name="more-resources"></a>Mais recursos

[Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto no serviço de aplicações do Azure (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto no serviço de aplicações do Azure para Linux](containers/tutorial-auth-aad.md)  
[Personalizar autenticação e autorização no serviço de aplicações](app-service-authentication-how-to.md)

Guias de procedimentos específica do fornecedor:

* [Como configurar a aplicação para utilizar o início de sessão do Azure Active Directory][AAD]
* [Como configurar a aplicação para utilizar o início de sessão do Facebook][Facebook]
* [Como configurar a aplicação para utilizar o início de sessão do Google][Google]
* [Como configurar a aplicação para utilizar o Microsoft Account login][MSA]
* [Como configurar a aplicação para utilizar o início de sessão do Twitter][Twitter]
* [How to: Utilizar a autenticação personalizada para a sua aplicação][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
