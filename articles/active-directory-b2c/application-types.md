---
title: Tipos de aplicação suportados por Azure AD B2C
titleSuffix: Azure AD B2C
description: Conheça os tipos de aplicações que pode utilizar com o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190148"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Tipos de aplicação que podem ser usados no Diretório Ativo B2C

O Azure Ative Directory B2C (Azure AD B2C) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas. Todos elas baseiam-se nos protocolos padrão da indústria [OAuth 2.0](protocols-overview.md) ou [OpenID Connect](protocols-overview.md). Este artigo descreve os tipos de aplicações que pode construir, independentemente do idioma ou plataforma que prefere. Também ajuda-o a compreender os cenários de alto nível antes de começar a construir aplicações.

Todas as aplicações que utilizem o Azure AD B2C devem ser registadas no seu [inquilino Azure AD B2C](tutorial-create-tenant.md) utilizando o [portal Azure](https://portal.azure.com/). O processo de registo de candidaturas recolhe e atribui valores, tais como:

* Um ID de **aplicação** que identifica exclusivamente a sua aplicação.
* Um **URL de resposta** que pode ser usado para direcionar as respostas de volta à sua aplicação.

Cada pedido enviado ao Azure AD B2C especifica um **fluxo de utilizador** (uma política incorporada) ou uma política **personalizada** que controla o comportamento do Azure AD B2C. Ambos os tipos de políticas permitem criar um conjunto altamente personalizável de experiências de utilizador.

A interação de cada aplicação segue um padrão de alto nível semelhante:

1. A aplicação direciona o utilizador para o ponto final v2.0 para executar uma [apólice](user-flow-overview.md).
2. O utilizador conclui a política de acordo com a sua definição.
3. O pedido recebe um símbolo de segurança do ponto final v2.0.
4. A aplicação utiliza o símbolo de segurança para aceder a informações protegidas ou a um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar que o acesso pode ser concedido.
6. A aplicação atualiza periodicamente o token de segurança.

Estes passos podem diferir ligeiramente com base no tipo de aplicação que está a construir.

## <a name="web-applications"></a>Aplicações Web

Para aplicações web (incluindo .NET, PHP, Java, Ruby, Python e Node.js) que são hospedadas num servidor e acessadas através de um navegador, o Azure AD B2C suporta o [OpenID Connect](protocols-overview.md) para todas as experiências do utilizador. Na implementação do OpenID Connect, a sua aplicação web inicia experiências de utilizador através da emissão de pedidos de autenticação para a AD Azure. O resultado do pedido é um `id_token`. Este token de segurança representa a identidade do utilizador. Também fornece informações sobre o utilizador na forma de afirmações:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Saiba mais sobre os tipos de fichas e reclamações disponíveis para uma aplicação na [referência simbólica Azure AD B2C](tokens-overview.md).

Numa aplicação web, cada execução de uma [política](user-flow-overview.md) toma estes passos de alto nível:

1. O utilizador navega na aplicação web.
2. A aplicação web redireciona o utilizador para o Azure AD B2C indicando a política de execução.
3. O utilizador completa a política.
4. Azure AD B2C `id_token` devolve um ao navegador.
5. O `id_token` é colocado no uri redirecionamento.
6. O `id_token` é validado e um cookie de sessão é definido.
7. Uma página segura é devolvida ao utilizador.

A validação do `id_token` utilizando uma chave pública de assinatura que é recebida do Azure AD é suficiente para verificar a identidade do utilizador. Este processo também define um cookie de sessão que pode ser usado para identificar o utilizador em pedidos de página subsequentes.

Para ver este cenário em ação, experimente uma das amostras de código de inscrição de aplicação web na nossa [secção Iniciar- Início](overview.md).

Além de facilitar o simples acesso, uma aplicação de servidor web também pode precisar de aceder a um serviço web back-end. Neste caso, a aplicação web pode executar um [fluxo openID connect](openid-connect.md) ligeiramente diferente e adquirir fichas usando códigos de autorização e fichas de atualização. Este cenário é descrito na seguinte [secção APIs da Web](#web-apis).

## <a name="web-apis"></a>APIs da Web

Pode utilizar o Azure AD B2C para garantir serviços web como a API web RESTful da sua aplicação. As APIs Web podem utilizar o OAuth 2.0 para proteger os seus dados, ao autenticar pedidos HTTP recebidos utilizando tokens. O autor da chamada de uma API web acrescenta um token no cabeçalho de autorização de um pedido HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API web pode utilizar o token para verificar a identidade do autor da chamada da API e extrair informações sobre o autor da chamada com base nas afirmações codificadas no token. Saiba mais sobre os tipos de tokens e afirmações disponíveis para uma aplicação na [referência de token do Azure AD B2C](tokens-overview.md).

Um API web pode receber fichas de muitos tipos de clientes, incluindo aplicações web, aplicações de desktop e mobile, aplicações de página única, daemons do lado do servidor e outras APIs web. Aqui está um exemplo do fluxo completo para uma aplicação web que chama uma API web:

1. A aplicação web executa uma política e o utilizador completa a experiência do utilizador.
2. O Azure AD B2C devolve `id_token` um (OpenID Connect) e um código de autorização para o navegador.
3. O navegador publica o `id_token` código e autorização para o redirecionamento DO URI.
4. O servidor web `id_token` valida o cookie de sessão.
5. O servidor web pede ao Azure `access_token` AD B2C um, fornecendo-lhe o código de autorização, identificação do cliente da aplicação e credenciais de cliente.
6. Os `access_token` `refresh_token` e são devolvidos ao servidor web.
7. A API web é `access_token` chamada com o cabeçalho de autorização.
8. A Web API valida o símbolo.
9. Os dados seguros são devolvidos à aplicação web.

Para obter mais informações sobre códigos de autorização, tokens de atualização e os passos para obter os tokens, leia sobre o [protocolo OAuth 2.0](authorization-code-flow.md).

Para saber como proteger uma API web utilizando o Azure AD B2C, consulte os tutoriais sobre web API na nossa [secção Introdução](overview.md).

## <a name="mobile-and-native-applications"></a>Aplicações móveis e nativas

As aplicações instaladas em dispositivos, como aplicações móveis e de desktop, muitas vezes precisam de aceder a serviços de back-end ou apis web em nome dos utilizadores. Você pode adicionar experiências personalizadas de gestão de identidade às suas aplicações nativas e ligar de forma segura para serviços back-end usando O Azure AD B2C e o fluxo de código de [autorização OAuth 2.0](authorization-code-flow.md).

Neste fluxo, a aplicação executa `authorization_code` [políticas](user-flow-overview.md) e recebe um ad da Azure após o utilizador completar a apólice. O `authorization_code` pedido representa a permissão do pedido para recorrer a serviços back-end em nome do utilizador que está atualmente inscrito. O pedido pode `authorization_code` então trocar `access_token` o `refresh_token`em segundo plano por um e um .  A aplicação `access_token` pode usar o para autenticar para uma API web back-end em pedidos HTTP. Também pode utilizar o `refresh_token` para obter um novo `access_token` quando o antigo expira.

## <a name="current-limitations"></a>Limitações atuais

### <a name="unsupported-application-types"></a>Tipos de aplicações não suportadas

#### <a name="daemonsserver-side-applications"></a>Aplicações do lado do daemons/servidor

As aplicações que contenham processos de longo prazo ou que funcionem sem a presença de um utilizador também precisam de uma forma de aceder a recursos seguros, como apis web. Estas aplicações podem autenticar e obter fichas utilizando a identidade da aplicação (em vez da identidade delegada de um utilizador) e utilizando o fluxo de credenciais de cliente OAuth 2.0. O fluxo de credenciais do cliente não é o mesmo que o fluxo em nome e o fluxo em nome não devem ser utilizados para a autenticação servidor-a-servidor.

Embora o fluxo de credenciais do cliente não seja atualmente suportado pelo Azure AD B2C, pode configurar o fluxo de credencial do cliente utilizando a AD Azure. Um inquilino Azure AD B2C partilha algumafuncionalidade com inquilinos empresariais da Azure AD.  O fluxo credencial do cliente é suportado utilizando a funcionalidade Azure AD do inquilino Azure AD B2C.

Para configurar o fluxo de credenciais de cliente, consulte [o Azure Ative Directory v2.0 e o fluxo de credenciais de cliente OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Uma autenticação bem sucedida resulta na receção de um símbolo formatado de modo a que possa ser utilizado pela Azure AD, tal como descrito na [referência token Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo em-nome-de)

Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, estando ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes nativos que têm um back-end Da Web API e chama um serviço online da Microsoft, como o Microsoft Graph API.

Este cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, também conhecido como fluxo em-nome-de.  No entanto, o fluxo em nome de não está atualmente implementado no Azure AD B2C.

### <a name="faulted-apps"></a>Aplicações com falha

Não edite aplicações Azure AD B2C desta forma:

- Em outros portais de gestão de aplicações, como o Portal de [Registo de Candidaturas.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
- Utilizando o Gráfico API ou PowerShell.

Se editar a aplicação Azure AD B2C fora do portal Azure, torna-se uma aplicação defeituosa e já não é utilizável com o Azure AD B2C. Apague a aplicação e crie-a novamente.

Para apagar a aplicação, vá ao Portal de Registo de [Aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) e elimine a aplicação. Para que a aplicação fique visível, tem de ser o proprietário da aplicação (e não apenas um administrador do inquilino).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas incorporadas fornecidas pelos [fluxos de utilizador no Diretório Ativo Azure B2C](user-flow-overview.md).
