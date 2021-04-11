---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: dd301cb3b18df5d3eb57ac38e9fb6432411d084b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073166"
---
#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/) 

1. Selecione **as inscrições da App** e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **Gestão**, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador As Minhas APIs.**
1. Selecione a API à qual a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. Em **Permissão,** expanda **a demonstração** e, em seguida, selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. **Selecione Permissões de adicionar**.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Se for solicitado para selecionar uma conta, selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud.*
1. Selecione **Yes** (Sim).
1. Selecione **Refresh**, e, em seguida, verifique que "Concedido para..." aparece em **Estado** para ambos os âmbitos.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Selecione **Aplicações (Legado)** e, em seguida, selecione a aplicação web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **o acesso a API** e, em seguida, selecione **Adicionar**.
1. No **dropdown Select API,** selecione a API a que a aplicação web deve ter acesso. Por exemplo, *webapi1*.
1. No **dropdown Select Scopes,** selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.
