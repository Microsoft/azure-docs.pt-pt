---
title: Como lidar com as alterações de cookies do SameSite no navegador Chrome [ Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com as alterações de cookies do SameSite no navegador Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534606"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Processar as alterações de cookies do SameSite no browser do Chrome

## <a name="what-is-samesite"></a>O que é sameSite?

`SameSite`é uma propriedade que pode ser definida em cookies HTTP para evitar ataques de falsificação de pedido de site cruzado (CSRF) em aplicações web:

- Quando `SameSite` é definido para **Lax,** o cookie é enviado em pedidos dentro do mesmo site e em pedidos GET de outros sites. Não é enviado em pedidos get que são de domínio cruzado.
- Um valor da **Strict** garante que o cookie é enviado apenas em pedidos dentro do mesmo site.

Por predefinição, o `SameSite` valor NÃO é definido nos navegadores e é por isso que não existem restrições aos cookies que estão a ser enviados em pedidos. Um pedido teria de optar pela proteção do CSRF, fixando **o Lax** ou **o Strict** de acordo com os seus requisitos.

## <a name="samesite-changes-and-impact-on-authentication"></a>Alterações no Mesmo Site e impacto na autenticação

Atualizações recentes [das normas no SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propõem `SameSite` proteger as aplicações, fazendo o comportamento padrão de quando nenhum valor é definido para Lax. Esta mitigação significa que os cookies serão restringidos em pedidos HTTP, exceto GET feito de outros sites. Além disso, é introduzido um valor de **None** para eliminar as restrições aos cookies enviados. Estas atualizações serão lançadas em breve numa próxima versão do navegador Chrome.

Quando as aplicações web autenticam com a plataforma Microsoft Identity utilizando o modo de resposta "form_post", o servidor de login responde à aplicação utilizando um POST HTTP para enviar os tokens ou código auth. Porque este pedido é um pedido `login.microsoftonline.com` de domínio transversal `https://contoso.com/auth`(do seu domínio - por exemplo ), cookies que foram definidos pela sua app agora se enquadram nas novas regras do Chrome. Os cookies que precisam de ser usados em cenários transversais são cookies que detêm os valores *do Estado* e *nonce,* que também são enviados no pedido de login. Há outros cookies deixados pela Azure AD para realizar a sessão.

Caso não atualize as suas aplicações web, este novo comportamento resultará em falhas de autenticação.

## <a name="mitigation-and-samples"></a>Mitigação e amostras

Para superar as falhas de autenticação, as aplicações web `SameSite` autenticando `None` com a plataforma de identidade da Microsoft podem definir a propriedade para cookies que são usados em cenários de domínio cruzado quando estão em execução no navegador Chrome.
Outros navegadores (ver [aqui](https://www.chromium.org/updates/same-site/incompatible-clients) para uma lista `SameSite` completa) seguem o comportamento `SameSite=None` anterior e não incluirão os cookies se estiver definido.
É por isso que, para suportar a autenticação em `SameSite` vários `None` navegadores, as aplicações web terão de definir o valor apenas no Chrome e deixar o valor vazio noutros navegadores.

Esta abordagem é demonstrada nas nossas amostras de código abaixo.

# <a name="net"></a>[.NET](#tab/dotnet)

A tabela abaixo apresenta os pedidos de pull que funcionaram em torno das alterações do SameSite nas nossas amostras ASP.NET e ASP.NET Core.

| Sample | Pedido Pull |
| ------ | ------------ |
|  [tutoriatutorial incremental da aplicação web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Conserção de cookies do mesmo site #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET amostra de aplicativo web MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Mesma #35 de correção de cookies do mesmo site](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [activo-directório-dotnet-admin-restrito-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Mesmo site cookie corrigir #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

para mais detalhes sobre como lidar com cookies do SameSite em ASP.NET e ASP.NET Core, consulte também:

- [Trabalhe com cookies SameSite em ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [blog ASP.NET no mesmo site](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Sample |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Sample | Pedido Pull |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Mesma #24 de correção de cookies do mesmo site](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Mesmo site corrigir cookie #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o MesmoSite e o cenário da aplicação Web:

> [!div class="nextstepaction"]
> [FaQ do Google Chrome no SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Página do mesmo site de Crómio](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Cenário: Web app que assina nos utilizadores](scenario-web-app-sign-user-overview.md)