---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456949"
---
Para ativar as palavras-passe detalhadas repor na sua aplicação, utilize um **reposição de palavra-passe** fluxo de utilizador. Tenha em atenção que a palavra-passe de ao nível do inquilino de reposição da opção é especificada [aqui](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Este fluxo de utilizador descreve as experiências que os clientes terão durante a reposição de palavra-passe e os conteúdos de tokens que a aplicação receberá após a conclusão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sob **Manage**, selecione **fluxos de utilizador** e clique em +**novo fluxo de utilizador**.

![Selecione o novo fluxo de utilizador](./media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Sobre o **recomendado** separador, selecione **reposição de palavra-passe**.

Introduza um fluxo de utilizador **nome** para a aplicação como referência. Por exemplo, introduza `SSPR`.

Sob **fornecedores de identidade**, verifique **repor a palavra-passe com endereço de e-mail**.

![Introduza o nome e selecione repor a palavra-passe com o endereço de e-mail como fornecedor de identidade](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Sob **afirmações de aplicação**, clique em **mostrar mais** e escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de experiência de reposição de uma palavra-passe com êxito. Por exemplo, selecione **ID de Objeto do Utilizador**.

Clique em **OK**.

![Selecione algumas afirmações de aplicação e clique no botão OK](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Clique em **criar** para adicionar o fluxo de utilizador. O fluxo de utilizador está listado como **B2C_1_SSPR**. O prefixo **B2C_1_** é acrescentado ao nome.

Clique em **executar o fluxo de utilizador**. Verifique as definições especificadas na tabela, em seguida, clique em **executar o fluxo de utilizador**.

![Selecione o fluxo de utilizador e executá-lo](./media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Definição      | Value  |
| ------------ | ------ |
| **Aplicação** | Aplicação Contoso B2C |
| **Selecionar o URL de resposta** | `https://localhost:44316/` |

É aberto um novo separador do browser e pode verificar a experiência de consumidor de reposição de palavras-passe na sua aplicação.

> [!NOTE]
> A criação da política e as atualizações demoram até um minuto a serem aplicadas.
>

