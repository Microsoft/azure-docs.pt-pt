---
title: Autenticação e autorização
description: Descubra sobre o suporte de autenticação e autorização incorporado no Azure App Service e Azure Functions, e como pode ajudar a proteger a sua aplicação contra o acesso não autorizado.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612760"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autenticação e autorização no Azure App Service e Azure Functions

O Azure App Service fornece capacidades de autenticação e autorização incorporadas (por vezes designadas como "Easy Auth"), para que possa assinar nos utilizadores e aceder aos dados escrevendo um código mínimo ou nenhum na sua aplicação web, API RESTful e back end móvel, e também [funções Azure.](../azure-functions/functions-overview.md) Este artigo descreve como o Serviço de Aplicações ajuda a simplificar a autenticação e a autorização para a sua aplicação.

## <a name="why-use-the-built-in-authentication"></a>Por que usar a autenticação incorporada?

Não é obrigado a utilizar esta funcionalidade para autenticação e autorização. Pode utilizar as funcionalidades de segurança agregadas na sua estrutura web de eleição, ou pode escrever os seus próprios utilitários. No entanto, terá de garantir que a sua solução se mantém atualizada com as mais recentes atualizações de segurança, protocolo e navegador.

Implementar uma solução segura para a autenticação (utilizadores de inscrição) e autorização (fornecer acesso a dados seguros) pode ter um esforço significativo. Deve certificar-se de seguir as melhores práticas e padrões da indústria e manter a sua implementação atualizada. A funcionalidade de autenticação incorporada para o Serviço de Aplicações e Funções Azure pode economizar tempo e esforço, fornecendo autenticação fora da caixa com fornecedores de identidade federados, permitindo-lhe focar-se no resto da sua aplicação.

- O Azure App Service permite-lhe integrar uma variedade de capacidades de auth na sua aplicação web ou API sem implementá-las por si mesmo.
- É incorporado diretamente na plataforma e não requer nenhuma linguagem em particular, SDK, conhecimentos de segurança, ou mesmo qualquer código para usar.
- Pode integrar-se com vários fornecedores de login. Por exemplo, AZure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Fornecedores de identidade

O Serviço de Aplicações utiliza [identidade federada,](https://en.wikipedia.org/wiki/Federated_identity)na qual um fornecedor de identidade de terceiros gere as identidades do utilizador e o fluxo de autenticação para si. Os seguintes fornecedores de identidade estão disponíveis por padrão:

| Fornecedor | Ponto final de inscrição | orientação How-To |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Início de sessão Azure AD do Serviço de Aplicações](configure-authentication-provider-aad.md) |
| [Conta Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [Início de conta microsoft do Serviço de Aplicações](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Início de sessão do Facebook do Serviço de Aplicações](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Serviço de aplicações Google login](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [Início de twitter do Serviço de Aplicações](configure-authentication-provider-twitter.md) |
| Qualquer fornecedor [de Ligação OpenID](https://openid.net/connect/) (pré-visualização) | `/.auth/login/<providerName>` | [Início de sessão OpenID Connect do Serviço de Aplicações](configure-authentication-provider-openid-connect.md) |

Quando permite a autenticação e autorização com um destes fornecedores, o seu ponto final de inscrição está disponível para autenticação do utilizador e para validação de fichas de autenticação do fornecedor. Pode fornecer aos seus utilizadores qualquer uma destas opções de inscrição.

## <a name="considerations-for-using-built-in-authentication"></a>Considerações para a utilização da autenticação incorporada

Ativar esta funcionalidade fará com que todos os pedidos da sua aplicação sejam automaticamente redirecionados para HTTPS, independentemente da configuração da configuração do Serviço de Aplicações para impor HTTPS. Pode desativar isto com a  `requireHttps` definição na configuração V2. No entanto, recomendamos que se mantenha com HTTPS, e deve garantir que nunca sejam transmitidos fichas de segurança através de ligações HTTP não seguras.

O Serviço de Aplicações pode ser utilizado para autenticação com ou sem restringir o acesso ao conteúdo do seu site e APIs. Para restringir o acesso da aplicação apenas aos utilizadores autenticados, desafie a ação a tomar quando o **pedido não for autenticado** para iniciar sessão com um dos fornecedores de identidade configurados. Para autenticar mas não restringir o acesso, detenhe **a ação a tomar quando o pedido não for autenticado** para "Permitir pedidos anónimos (sem ação)."

> [!NOTE]
> Deve dar a cada registo de aplicações a sua própria permissão e consentimento. Evite a partilha de permissão entre ambientes utilizando registos de aplicações separados para slots de implantação separados. Ao testar um novo código, esta prática pode ajudar a evitar que os problemas afetem a aplicação de produção.

## <a name="how-it-works"></a>Como funciona

[Arquitetura de recursos no Windows (implantação não contentor)](#feature-architecture-on-windows-non-container-deployment))

[Arquitetura de recurso em Linux e contentores](#feature-architecture-on-linux-and-containers)

[Fluxo de autenticação](#authentication-flow)

[Comportamento da autorização](#authorization-behavior)

[Reclamações de utilizador e aplicação](#user-and-application-claims)

[Arquivo de tokens](#token-store)

[Registo e rastreio](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Arquitetura de recursos no Windows (implantação não contentor)

O módulo de autenticação e autorização funciona na mesma caixa de areia que o seu código de aplicação. Quando está ativado, todos os pedidos HTTP de entrada passam por ele antes de serem tratados pelo código de aplicação.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Um diagrama de arquitetura que mostra pedidos sendo intercetados por um processo na caixa de areia do site que interage com fornecedores de identidade antes de permitir o tráfego para o site implantado" lightbox="media/app-service-authentication-overview/architecture.png":::

Este módulo lida com várias coisas para a sua aplicação:

- Autentica os utilizadores com o fornecedor especificado
- Valida, lojas e refreshes tokens
- Gere a sessão autenticada
- Injeta informação de identidade em cabeçalhos de pedido

O módulo funciona separadamente do seu código de aplicação e é configurado usando as definições de aplicações. Não são necessários SDKs, linguagens específicas ou alterações ao seu código de aplicação. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Arquitetura de recurso em Linux e contentores

O módulo de autenticação e autorização funciona num recipiente separado, isolado do seu código de aplicação. Utilizando o que é conhecido como o [padrão Ambassador,](/azure/architecture/patterns/ambassador)interage com o tráfego de entrada para executar funcionalidades semelhantes às do Windows. Uma vez que não funciona em processo, não é possível uma integração direta com quadros linguísticos específicos; no entanto, as informações relevantes de que a sua aplicação necessita são transmitidas através de cabeçalhos de pedido, conforme explicado abaixo.

#### <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os fornecedores, mas difere consoante pretenda iniciar súbência com o SDK do fornecedor:

- Sem provedor SDK: Os delegados de aplicação federaram o s-in no Serviço de Aplicações. Este é normalmente o caso das aplicações do navegador, que podem apresentar a página de login do fornecedor ao utilizador. O código do servidor gere o processo de inscrição, pelo que também é chamado _de fluxo direcionado para o servidor_ ou fluxo do _servidor_. Este caso aplica-se a aplicações de navegador. Aplica-se também a aplicações nativas que assinam os utilizadores na utilização do cliente de Aplicações Móveis SDK porque o SDK abre uma visão web para iniciar súmido com a autenticação do Serviço de Aplicações.
- Com o fornecedor SDK: A aplicação assina os utilizadores no fornecedor manualmente e, em seguida, submete o token de autenticação ao Serviço de Aplicações para validação. Este é normalmente o caso de aplicações sem navegador, que não podem apresentar a página de início de sção do fornecedor ao utilizador. O código de aplicação gere o processo de inscrição, pelo que também é chamado _de fluxo dirigido ao cliente_ ou fluxo de _clientes._ Este caso aplica-se a CLIENTES DE REST APIs, [Azure Functions](../azure-functions/functions-overview.md)e JavaScript, bem como aplicações de navegador que precisam de mais flexibilidade no processo de login. Aplica-se também a aplicações móveis nativas que assinam os utilizadores na utilização do SDK do fornecedor.

As chamadas de uma aplicação de navegador fidedigna no Serviço de Aplicações para outra API REST no Serviço de Aplicações ou [Funções Azure](../azure-functions/functions-overview.md) podem ser autenticadas usando o fluxo dirigido pelo servidor. Para mais informações, consulte [Personalizar a autenticação e autorização no Serviço de Aplicações.](app-service-authentication-how-to.md)

A tabela abaixo mostra os passos do fluxo de autenticação.

| Passo | Sem fornecedor SDK | Com fornecedor SDK |
| - | - | - |
| 1. Iniciar s inscrição no utilizador | Redireciona o cliente para `/.auth/login/<provider>` . | O código do cliente assina diretamente com o SDK do fornecedor e recebe um token de autenticação. Para obter informações, consulte a documentação do fornecedor. |
| 2. Pós-autenticação | O fornecedor redireciona o cliente para `/.auth/login/<provider>/callback` . | O código do cliente [publica o token do fornecedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` validação. |
| 3. Estabelecer sessão autenticada | O Serviço de Aplicações adiciona cookie autenticado à resposta. | O Serviço de Aplicações devolve o seu próprio token de autenticação ao código do cliente. |
| 4. Servir conteúdo autenticado | O cliente inclui cookie de autenticação em pedidos posteriores (tratado automaticamente por navegador). | O código do cliente apresenta um token de autenticação no `X-ZUMO-AUTH` cabeçalho (manuseado automaticamente por SDKs cliente de Aplicações Móveis). |

Para os navegadores de clientes, o Serviço de Aplicações pode automaticamente direcionar todos os utilizadores não autenticados para `/.auth/login/<provider>` . Também pode apresentar aos utilizadores um ou mais `/.auth/login/<provider>` links para iniciar sôm na sua app utilizando o seu fornecedor de eleição.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Comportamento da autorização

No [portal Azure,](https://portal.azure.com)pode configurar a autorização do Serviço de Aplicações com vários comportamentos quando o pedido de entrada não for autenticado.

![Uma imagem mostrando o dropdown "Action to take when request is not autenticado"](media/app-service-authentication-overview/authorization-flow.png)

Os seguintes títulos descrevem as opções.

**Permitir pedidos anónimos (sem ação)**

Esta opção adia a autorização de tráfego não autenticado para o seu código de aplicação. Para pedidos autenticados, o Serviço de Aplicações também transmite informações de autenticação nos cabeçalhos HTTP.

Esta opção proporciona mais flexibilidade no tratamento de pedidos anónimos. Por exemplo, permite apresentar [vários fornecedores de inscrição](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos seus utilizadores. No entanto, tem de escrever código.

**Permitir apenas pedidos autenticados**

A opção é **iniciar sessão com \<provider>**. O Serviço de Aplicações redireciona todos os pedidos anónimos `/.auth/login/<provider>` para o fornecedor que escolher. Se o pedido anónimo vier de uma aplicação móvel nativa, a resposta devolvida é uma `HTTP 401 Unauthorized` .

Com esta opção, não precisa de escrever nenhum código de autenticação na sua aplicação. A autorização mais fina, como a autorização específica para a função, pode ser tratada inspecionando as reclamações do utilizador (ver [pedidos do utilizador do Access).](app-service-authentication-how-to.md#access-user-claims)

> [!CAUTION]
> Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que pretendam uma página inicial disponível ao público, como em muitas aplicações de uma página única.

> [!NOTE]
> Por predefinição, qualquer utilizador do seu inquilino Azure AD pode solicitar um sinal para a sua aplicação a Azure AD. Pode [configurar a aplicação em Azure AD](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) se quiser restringir o acesso à sua aplicação a um conjunto definido de utilizadores.


#### <a name="user-and-application-claims"></a>Reclamações de utilizador e aplicação

Para todos os quadros linguísticos, o Serviço de Aplicações coloca as reclamações no token de entrada (seja de um utilizador final autenticado ou de uma aplicação de cliente) disponível para o seu código, injetando-as nos cabeçalhos de pedido. Para ASP.NET aplicações 4.6, o Serviço de Aplicações povoa [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as reclamações do utilizador autenticado, para que possa seguir o padrão de código .NET, incluindo o `[Authorize]` atributo. Da mesma forma, para aplicações PHP, o Serviço de Aplicações povoa a `_SERVER['REMOTE_USER']` variável. Para aplicações Java, as reclamações estão [acessíveis a partir do servlet Tomcat.](configure-language-java.md#authenticate-users-easy-auth)

Para [funções Azure](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` não é preenchido para código .NET, mas ainda pode encontrar as reclamações do utilizador nos cabeçalhos de pedido, ou obter o objeto do contexto de pedido ou mesmo através de um parâmetro de `ClaimsPrincipal` ligação. Consulte [o trabalho com as identidades dos clientes](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) para obter mais informações.

Para obter mais informações, consulte [as reclamações do utilizador do Access.](app-service-authentication-how-to.md#access-user-claims)

Neste momento, ASP.NET Core não suporta atualmente a população do utilizador atual com a funcionalidade Autenticação/Autorização. No entanto, existem cerca [de 3º partido, existem componentes de middleware de código aberto](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) para ajudar a preencher esta lacuna.

#### <a name="token-store"></a>Arquivo de tokens

O App Service fornece uma loja de tokens incorporada, que é um repositório de fichas que estão associadas aos utilizadores das suas aplicações web, APIs ou aplicações móveis nativas. Quando ativa a autenticação com qualquer fornecedor, esta loja de token está imediatamente disponível para a sua aplicação. Se o seu código de aplicação necessitar de aceder aos dados destes fornecedores em nome do utilizador, tais como:

- post na linha temporal do Facebook do utilizador autenticado
- ler os dados corporativos do utilizador usando a API do Gráfico microsoft

Normalmente, deve escrever código para recolher, armazenar e refrescar estes tokens na sua aplicação. Com a loja de [fichas, basta recuperar os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar e [dizer ao Serviço de Aplicações para os refrescar](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando ficarem inválidos. 

Os tokens de ID, tokens de acesso e tokens de atualização estão em cache para a sessão autenticada, e são acessíveis apenas pelo utilizador associado.  

Se não precisar de trabalhar com fichas na sua aplicação, pode desativar a loja simbólica na página de **Autenticação/Autorização** da sua aplicação.

#### <a name="logging-and-tracing"></a>Registo e rastreio

Se [ativar a sessão de registo de aplicações,](troubleshoot-diagnostic-logs.md)verá vestígios de autenticação e autorização diretamente nos seus ficheiros de registo. Se vir um erro de autenticação que não esperava, pode convenientemente encontrar todos os detalhes olhando para os registos de aplicações existentes. Se ativar [o rastreio de pedidos falhados,](troubleshoot-diagnostic-logs.md)pode ver exatamente qual o papel que o módulo de autenticação e autorização poderá ter desempenhado num pedido falhado. Nos registos de vestígios, procure referências a um módulo chamado `EasyAuthModule_32/64` .

## <a name="more-resources"></a>Mais recursos

- [Como: Configurar o seu Serviço de Aplicações ou app Azure Functions para utilizar o login AZure AD](configure-authentication-provider-aad.md)
- [Uso avançado da autenticação e autorização no Serviço de Aplicações Azure](app-service-authentication-how-to.md)

Amostras:
- [Tutorial: Adicione autenticação à sua aplicação web em execução no Azure App Service](scenario-secure-app-authentication-app-service.md)
- [Tutorial: Autenticar e autorizar os utilizadores de ponta a ponta no Azure App Service (Windows ou Linux)](tutorial-auth-aad.md)
- [.NET Integração central do Azure AppService EasyAuth (3º partido)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Obtenção de autenticação do Azure App Service trabalhando com .NET Core (3º partido)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
