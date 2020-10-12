---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbf1daf3a70e5ca87b4a0027b53aed5fab5691d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84317782"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregtraining)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *testapp1*.
1. Selecione **Contas em qualquer diretório organizacional ou qualquer fornecedor de identidade.**
1. Em **URI de redirecionamento,** selecione **Web**e, em seguida, `https://jwt.ms` introduza na caixa de texto URL.
1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.

Uma vez concluído o registo do pedido, permita o fluxo de subvenção implícito:

1. Em **Gestão**, **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a concessão implícita,** selecione as caixas de verificação **de fichas de acesso** e **id.**
1. Selecione **Guardar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações**e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *testapp1*.
1. Para **Aplicação Web / Web API,** selecione **Sim**.
1. Para **URL de resposta**, insira `https://jwt.ms`
1. Selecione **Criar**.