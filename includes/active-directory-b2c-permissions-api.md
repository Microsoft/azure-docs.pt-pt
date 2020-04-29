---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875697"
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
1. **Selecione Adicionar permissões**.
1. Selecione **Grant administrador consentimento para (o nome do seu inquilino)**.
1. Se for solicitado a selecionar uma conta, selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que tenha sido atribuída pelo menos a função de administrador de *aplicação Cloud.*
1. Selecione **Sim**.
1. Selecione **Refresh**, e, em seguida, verifique se "Granted for ..." aparece em **Status** para ambos os âmbitos.
