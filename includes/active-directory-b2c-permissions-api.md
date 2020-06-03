---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: e37419b5d568b0f60e4c6a0c8fed26eb418f501e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297496"
---
#### <a name="app-registrations-preview"></a>[Inscrições de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione **registos de Aplicações (Pré-visualização)** e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **Gestão**, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador As Minhas APIs.**
1. Selecione a API à qual a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. Em **Permissão,** expanda **a demonstração**e, em seguida, selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. **Selecione Permissões de adicionar**.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Se for solicitado para selecionar uma conta, selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud.*
1. Selecione **Sim**.
1. Selecione **Refresh**, e, em seguida, verifique que "Concedido para..." aparece em **Estado** para ambos os âmbitos.

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Selecione **Aplicações**e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **o acesso a API**e, em seguida, selecione **Adicionar**.
1. No **dropdown Select API,** selecione a API a que a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. No **dropdown Select Scopes,** selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.
