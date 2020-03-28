---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186151"
---
#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Selecione **Aplicações**e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **o acesso API,** e, em seguida, selecione **Adicionar**.
1. No downdown **Select API,** selecione a API a que a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. No **dropdown Select Scopes,** selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione registos de **aplicações (Pré-visualização)** e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **Gerir,** selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicionar uma permissão**.
1. Selecione o separador **My APIs.**
1. Selecione a API a que a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. Sob **Permissão,** expanda a **demonstração**e, em seguida, selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. **Selecione Adicionar permissões**. Como foi dirigido, aguarde alguns minutos antes de passar para o próximo passo.
1. Selecione **Grant administrador consentimento para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que tenha sido atribuída pelo menos a função de administrador de *aplicação Cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique se "Granted for ..." aparece em **Status** para ambos os âmbitos. Pode levar alguns minutos para as permissões se propagarem.