---
title: Adicione uma aplicação web API - Azure Ative Directory B2C / Microsoft Docs
description: Saiba como adicionar uma aplicação web API ao seu inquilino Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949824"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicione uma aplicação web API ao seu inquilino Azure Ative Directory B2C

 Registe os recursos da API web no seu inquilino para que possam aceitar e responder aos pedidos de aplicações do cliente que apresentem um token de acesso. Este artigo mostra-lhe como registar uma API web em Azure Ative Directory B2C (Azure AD B2C).

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *webapi1*.
1. Em **Redirect URI**, selecione **Web**, e, em seguida, introduza um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que a sua candidatura solicite. Numa aplicação de produção, pode definir o URI de redirecionamento como `https://localhost:5000` . Durante o desenvolvimento ou teste, pode `https://jwt.ms` defini-lo para , uma aplicação web de propriedade da Microsoft que exibe o conteúdo descodificado de um token (o conteúdo do token nunca sai do seu navegador). Pode adicionar e modificar URIs redirecionando as suas aplicações registadas a qualquer momento.
1. Selecione **Registar**.
1. Grave o **ID de Aplicação (cliente)** para utilização no código da API da sua web.

Se tiver uma aplicação que implemente o fluxo de subvenção implícito, por exemplo, uma [aplicação de página única baseada em JavaScript (SPA),](tutorial-register-spa.md)pode ativar o fluxo seguindo estes passos:

1. Em **Gestão**, **selecione Autenticação**.
1. Sob **a concessão implícita,** selecione as caixas de verificação **de fichas de acesso** e **id.**
1. Selecione **Guardar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir aplicativo web/web API** e **permitir fluxo implícito,** selecione **Sim**.
7. Para **URL de resposta,** insira um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que o seu pedido de candidatura. Na sua aplicação de produção, poderá definir o URL de resposta para um valor como `https://localhost:44332` . Para efeitos de teste, desa um pouco para o URL de resposta `https://jwt.ms` .
8. Para **app ID URI,** insira o identificador utilizado para a sua API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, grave o ID da aplicação que utilizará quando configurar a aplicação web.

* * *

## <a name="configure-scopes"></a>Configure âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de uma aplicação, precisa conceder as permissões de sua candidatura à API. Por exemplo, em [Tutorial: Registar uma aplicação no Azure Ative Directory B2C,](tutorial-register-applications.md)uma aplicação web chamada *webapp1* está registada em Azure AD B2C. Pode utilizar esta aplicação para ligar para a API web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação está registada para ligar para a API web protegida. Um utilizador autentica-se com Azure AD B2C para utilizar a aplicação. O pedido obtém uma autorização da Azure AD B2C para aceder à API web protegida.