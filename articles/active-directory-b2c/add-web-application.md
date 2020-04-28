---
title: Adicione uma aplicação Web API - Azure Ative Directory B2C [ Microsoft Docs
description: Saiba como adicionar uma aplicação Web API ao seu inquilino Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190182"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicione uma aplicação Web API ao seu inquilino Azure Ative Directory B2C

 Registe os recursos da Web API no seu inquilino para que possam aceitar e responder a pedidos de pedidos de clientes que apresentem um sinal de acesso. Este artigo mostra-lhe como registar uma API web no Azure Ative Directory B2C (Azure AD B2C).

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir web app/ web API** e **permitir fluxo implícito,** selecione **Sim**.
7. Para **o URL de resposta,** insira um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Na sua aplicação de produção, poderá definir `https://localhost:44332`o URL de resposta para um valor como . Para efeitos de teste, delineie o URL de resposta para `https://jwt.ms`.
8. Para **o ID da aplicação URI,** introduza o identificador utilizado para a sua Web API. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, grave o ID da aplicação que utilizará quando configurar a aplicação web.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *webapi1*.
1. Em **Redirecione o URI**, selecione **Web**e, em seguida, introduza um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Numa aplicação de produção, pode definir o `https://localhost:5000`URI redirecionamento de um ponto final como . Durante o desenvolvimento ou teste, `https://jwt.ms`pode defini-lo para , uma aplicação web propriedade da Microsoft que exibe os conteúdos descodificados de um token (o conteúdo do token nunca sai do seu navegador). Pode adicionar e modificar URIs redirecionados nas suas aplicações registadas a qualquer momento.
1. Selecione **Registar**.
1. Grave o ID de **Aplicação (cliente)** para utilização no código da API web.

Se tiver uma aplicação que implemente o fluxo implícito de subvenção, por exemplo, uma aplicação de uma página única baseada no JavaScript (SPA), pode ativar o fluxo seguindo estes passos:

1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a subvenção Implícita,** selecione tanto as **fichas** de acesso como as **fichas de identificação** das caixas de verificação.
1. Selecione **Guardar**.

* * *

## <a name="configure-scopes"></a>Configurar os âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de uma aplicação, você precisa conceder as suas permissões de pedido para a API. Por exemplo, no [Tutorial: Registe uma aplicação no Azure Ative Directory B2C,](tutorial-register-applications.md)uma aplicação web chamada *webapp1* está registada no Azure AD B2C. Pode utilizar esta aplicação para chamar a Web API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação está registada para chamar a API da web protegida. Um utilizador autentica com Azure AD B2C para utilizar a aplicação. O pedido obtém uma autorização do Azure AD B2C para aceder à API web protegida.
