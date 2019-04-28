---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/03/2016
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128218"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registar a aplicação Web, utilize as definições especificadas na tabela.

![Definições de registo de exemplo para a nova aplicação Web](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Definição      | Valor da amostra  | Descrição                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | Aplicação Contoso B2C | Introduza um **Nome** para a aplicação que descreva a aplicação aos consumidores. | 
| **Incluir aplicação/API Web** | Sim | Selecione **Sim** para uma aplicação Web. |
| **Permitir fluxo implícito** | Sim | Selecione **Sim** se a sua aplicação utilizar o [Início de sessão OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **URL de resposta** | `https://localhost:44316` | Os URLs de resposta são pontos finais para onde o Azure AD B2C devolve quaisquer tokens que a aplicação solicite. Introduza [um](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **URL de resposta** adequado. Neste exemplo, a aplicação é local e está a escutar na porta 44316. |

Clique em **Criar** para registar a aplicação.

A aplicação recentemente registada é apresentada na lista de aplicações para o inquilino do B2C. Selecione a aplicação Web na lista. É apresentado o painel de propriedades da aplicação Web.

![Propriedades da aplicação Web](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Anote o **ID de Cliente da Aplicação** globalmente exclusivo. Utilize o ID no código da aplicação.

