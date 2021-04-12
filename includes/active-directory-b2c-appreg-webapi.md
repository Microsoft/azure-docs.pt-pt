---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 7cbf6ca7606210b70ae9efa69a8cc6551eb6f36f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072874"
---
Os recursos da Web API precisam de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações de clientes que apresentem um token de acesso.

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *webapi1*.
1. Em **Redirect URI**, selecione **Web**, e, em seguida, introduza um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que a sua candidatura solicite. Neste tutorial, a amostra corre localmente e `http://localhost:5000` ouve.
1. Selecione **Registar**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.

Em seguida, ativar o fluxo de subvenção implícita:

1. Em **Gestão**, **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a concessão implícita,** selecione as caixas de verificação **de fichas de acesso** e **id.**
1. Selecione **Guardar**.

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
1. Para **Aplicação Web / Web API,** selecione **Sim**.
1. Para **Permitir o fluxo implícito,** selecione **Sim**.
1. Para **URL de resposta,** insira um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que o seu pedido de candidatura. Neste tutorial, a amostra corre localmente e `https://localhost:5000` ouve.
1. Para **app ID URI,** adicione um identificador de ponto final API ao URI mostrado. Para este tutorial, `api` entre, de modo a que o URI completo seja semelhante a `https://contosob2c.onmicrosoft.com/api` .
1. Selecione **Criar**.
1. Grave o **ID APPLICATION** para utilização num passo posterior.