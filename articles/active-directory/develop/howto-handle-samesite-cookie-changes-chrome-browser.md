---
title: Como lidar com as alterações de cookies SameSite no navegador Chrome Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com as alterações de cookies SameSite no navegador Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 5742ddf9553c3ac9187dbef93fc7927564cbc095
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116976"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Processar as alterações de cookies do SameSite no browser do Chrome

## <a name="what-is-samesite"></a>O que é SameSite?

`SameSite` é uma propriedade que pode ser configurada em cookies HTTP para evitar ataques de falsificação de pedidos de cross site (CSRF) em aplicações web:

- Quando `SameSite` está definido para **Lax,** o cookie é enviado em pedidos dentro do mesmo site e em pedidos GET de outros sites. Não é enviado em pedidos GET que são de domínio transversal.
- Um valor da **Strict** garante que o cookie é enviado em pedidos apenas dentro do mesmo site.

Por padrão, o `SameSite` valor NÃO está definido nos navegadores e é por isso que não existem restrições sobre cookies que sejam enviados em pedidos. Um pedido teria de optar pela proteção do CSRF, definindo **lax ou** **rígido** de acordo com os seus requisitos.

## <a name="samesite-changes-and-impact-on-authentication"></a>Alterações no SameSite e impacto na autenticação

As recentes [atualizações às normas no SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propõem proteger as aplicações, fazendo o comportamento padrão de `SameSite` quando nenhum valor é definido para Lax. Esta mitigação significa que os cookies serão restringidos em pedidos HTTP, exceto GET feito a partir de outros sites. Além disso, é introduzido um valor de **Nenhum** para remover as restrições aos cookies que são enviados. Estas atualizações serão lançadas em breve numa próxima versão do navegador Chrome.

Quando as aplicações web autenticam-se com a plataforma Microsoft Identity utilizando o modo de resposta "form_post", o servidor de login responde à aplicação utilizando um HTTP POST para enviar os tokens ou código de auth. Uma vez que este pedido é um pedido de domínio transversal (do `login.microsoftonline.com` seu domínio - por exemplo - os `https://contoso.com/auth` cookies que foram definidos pela sua app estão agora de acordo com as novas regras do Chrome. Os cookies que precisam de ser usados em cenários de cross-site são cookies que detêm o *estado* e valores *nonce,* que também são enviados no pedido de login. Há outros cookies deixados pela Azure AD para realizar a sessão.

Se não atualizar as suas aplicações web, este novo comportamento resultará em falhas de autenticação.

## <a name="mitigation-and-samples"></a>Mitigação e amostras

Para superar as falhas de autenticação, as aplicações web autenticadas com a plataforma de identidade da Microsoft podem definir `SameSite` a propriedade `None` para cookies que são utilizados em cenários de domínio transversal quando estão a correr no navegador Chrome.
Outros navegadores (consulte [aqui](https://www.chromium.org/updates/same-site/incompatible-clients) para uma lista completa) seguem o comportamento anterior `SameSite` e não incluirão os cookies se `SameSite=None` estiverem definidos.
Por isso, para suportar a autenticação em vários navegadores, as aplicações web terão de definir o `SameSite` valor `None` apenas no Chrome e deixar o valor vazio em outros navegadores.

Esta abordagem está demonstrada nas nossas amostras de código abaixo.

# <a name="net"></a>[.NET](#tab/dotnet)

A tabela abaixo apresenta os pedidos de puxar que trabalharam em torno das alterações do SameSite nas nossas amostras de ASP.NET e ASP.NET Core.

| Sample | Pedido Pull |
| ------ | ------------ |
|  [tutorial incremental da aplicação web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [A mesma correção de cookies do site #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET amostra de aplicativo web MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [A mesma correção de cookies do site #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directy-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [A mesma correção de cookies do site #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

para mais detalhes sobre como lidar com os cookies SameSite em ASP.NET e ASP.NET Core, consulte também:

- [Trabalhe com cookies SameSite em ASP.NET Core](/aspnet/core/security/samesite) .
- [ASP.NET Blog sobre a edição do SameSite](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Sample |
| ------ |
|  [ms-identidade-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Sample | Pedido Pull |
| ------ | ------------ |
|  [ms-identidade-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [A mesma correção de cookies do site #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identidade-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [A mesma correção de cookies do site #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o SameSite e o cenário da aplicação Web:

> [!div class="nextstepaction"]
> [FAQ do Google Chrome no SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Página SameSite de Crómio](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md)