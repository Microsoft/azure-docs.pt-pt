---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 38261556579fb5db86fb231b0edf3412eed51d35
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072989"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *ROPC_Auth_app.*
1. Deixe os outros valores como estão e, em seguida, **selecione Registar**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.
1. Em **Gestão**, **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **o tipo de cliente predefinido,** selecione **Sim** para tratar a aplicação como um cliente público. Esta definição é necessária para o fluxo ROPC.
1. Selecione **Guardar**.
1. No menu esquerdo, selecione **Manifesto** para abrir o editor manifesto. 
1. Deite o **atributo Oauth2AllowImplicitFlow** a *verdadeiro*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Selecione **Guardar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *ROPC_Auth_app.*
1. Para **cliente nativo,** selecione **Sim**.
1. Deixe os outros valores como estão e, em seguida, **selecione Criar**.
1. Grave o **ID APPLICATION** para utilização num passo posterior.