---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455659"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sob **Manage**, selecione **fluxos de utilizador** e clique em +**novo fluxo de utilizador**.

![Selecione o novo fluxo de utilizador](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Sobre o **recomendado** separador, selecione **inscrever-se e iniciar sessão**.

![Selecione o sinal de cópia de segurança e inicie sessão no fluxo de utilizador](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Introduza um fluxo de utilizador **nome** para a aplicação como referência. Por exemplo, introduza `SiUpIn`.

Sob **fornecedores de identidade** e verifique **inscrição de E-Mail**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados.

Sob **a autenticação Multifator**, escolha o **ativado** ou **desativado**.

![Introduza um nome e selecione inscrição de E-Mail como fornecedor de identidade](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

Sob **atributos de utilizador e afirmações**, selecione **mostrar mais** para ver a lista completa de atributos e pode escolher a partir de afirmações.

Na **atributo recolher** coluna, escolha os atributos que quer recolher do consumidor durante a inscrição. Por exemplo, selecione **País/Região**, **Nome a Apresentar**, e **Código Postal**.

Na **declaração de retorna** coluna, escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de uma experiência de inscrição ou início de sessão com êxito. Por exemplo, selecione **Nome a Apresentar**, **Fornecedor de Identidade**, **Código Postal**, **O utilizador é novo** e **ID de Objeto do Utilizador**.

Clique em **OK**.

![Selecione alguns atributos de utilizador e afirmações e clique no botão OK](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Clique em **criar** para adicionar o fluxo de utilizador. O fluxo de utilizador está listado como **B2C_1_SiUpIn**. O prefixo **B2C_1_** é acrescentado ao nome.

Selecione **executar o fluxo de utilizador**. Verifique as definições especificadas na tabela, em seguida, clique em **executar o fluxo de utilizador**.

![Selecione o fluxo de utilizador de execução](./media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Definição      | Value  |
| ------------ | ------ |
| **Aplicação** | Aplicação Contoso B2C |
| **URL de resposta** | `https://localhost:44316/` |

É aberto um novo separador do browser e pode verificar a experiência de consumidor de inscrição ou início de sessão, conforme configurado.

> [!NOTE]
> Demoram até um minuto para criação de fluxos de utilizador e atualizações em vigor.
>

