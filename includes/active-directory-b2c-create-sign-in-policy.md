---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456035"
---
Se pretender ativar apenas o início de sessão na sua aplicação, utilize um **início de sessão** fluxo de utilizador. Este fluxo de utilizador descreve as experiências que os clientes terão durante o início de sessão e os conteúdos de tokens que a aplicação receberá nos inícios de sessão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Sob **Manage**, selecione **fluxos de utilizador**.

Clique em +**novo fluxo de utilizador** na parte superior do painel.

Sob **selecionar um tipo de fluxo de utilizador**, selecione **todos os**e, em seguida, selecione a versão do **entrar** que pretende utilizar.

O **nome** determina o nome do fluxo de utilizador de início de sessão utilizado pela sua aplicação. Por exemplo, introduza **SiIn**.

Sob **fornecedores de identidade**, selecione uma opção. Também pode selecionar fornecedores de identidade social, se já configurado. Clique em **OK**.

Sob **afirmações de aplicação**, clique em **mostrar mais**.

Na **declaração de retorna** coluna, escolher as afirmações que quer que sejam devolvidas nos tokens enviados para a sua aplicação depois de uma experiência de início de sessão com êxito. Por exemplo, selecione **Nome a Apresentar**, **Fornecedor de Identidade**, **Código Postal** e **ID de Objeto do Utilizador**. Clique em **OK**.

Clique em **Criar**. Tenha em atenção que o fluxo de utilizador que acabou de criar é apresentado como **B2C_1_SiIn** (o **B2C\_1\_**  fragmento é adicionado automaticamente).

Clique em **executar o fluxo de utilizador**.

Selecione **aplicação Contoso B2C** no **aplicativo** menu pendente e `https://localhost:44321/` no **URL de resposta** pendente.

Clique em **executar o fluxo de utilizador**. É aberto um novo separador do browser e pode percorrer a experiência de consumidor de início de sessão na sua aplicação.

> [!NOTE]
> Demoram até um minuto para criação de fluxos de utilizador e atualizações em vigor.
>

