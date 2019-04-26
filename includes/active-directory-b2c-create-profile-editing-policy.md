---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456043"
---
Se pretender ativar a edição de perfil na sua aplicação, utilize um **edição de perfil** fluxo de utilizador. Este fluxo de utilizador descreve as experiências que os clientes terão durante a edição de perfis e os conteúdos de tokens que a aplicação receberá após a conclusão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sob **Manage**, selecione **fluxos de utilizador** e clique em +**novo fluxo de utilizador**.

![Selecione o novo fluxo de utilizador](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Sobre o **recomendado** separador, selecione **edição de perfil**.

Introduza um fluxo de utilizador **nome** para a aplicação como referência. Por exemplo, introduza `SiPe`.

Sob **fornecedores de identidade**, verifique **início de sessão de conta Local**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados.

![Selecione Início de Sessão da Conta como fornecedor de identidade e clique no botão OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

Sob **atributos de utilizador**, clique em **mostrar mais**. Na **atributo recolher** coluna, escolha os atributos, o consumidor pode ver e editar no respetivo perfil. Por exemplo, selecione **País/Região**, **Nome a Apresentar**, e **Código Postal**.

Na **declaração de retorna** coluna, escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de uma experiência de edição de perfis com êxito. Por exemplo, selecione **Nome a Apresentar**, **Código Postal**.

Clique em **OK**.

![Selecione algumas afirmações de aplicação e clique no botão OK](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Clique em **criar** para adicionar o fluxo de utilizador. O fluxo de utilizador está listado como **B2C_1_SiPe**. O prefixo **B2C_1_** é acrescentado ao nome.

Selecione **executar o fluxo de utilizador**. Verifique as definições especificadas na tabela, em seguida, clique em **executar o fluxo de utilizador**.

![Selecione o fluxo de utilizador e executá-lo](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Definição      | Value  |
| ------------ | ------ |
| **Aplicação** | Aplicação Contoso B2C |
| **URL de resposta** | `https://localhost:44316/` |

É aberto um novo separador do browser e pode verificar a experiência de consumidor de edição de perfis, conforme configurado.

> [!NOTE]
> Demoram até um minuto para criação de fluxos de utilizador e atualizações em vigor.
>

