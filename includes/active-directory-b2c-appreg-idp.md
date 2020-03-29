---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186369"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *testapp1*.
1. Para **web app / Web API,** selecione **Sim**.
1. Para **url de resposta,** insira`https://jwt.ms`
1. Selecione **Criar**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *testapp1*.
1. Selecione **Contas em qualquer diretório organizacional ou qualquer fornecedor**de identidade.
1. Em **Redirecione o URI,** selecione **Web**e, em seguida, introduza `https://jwt.ms` na caixa de texto URL.
1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.

Uma vez concluído o registo de candidatura, ative o fluxo implícito de subvenção:

1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a subvenção Implícita,** selecione tanto as **fichas** de acesso como as **fichas de identificação** das caixas de verificação.
1. Selecione **Guardar**.