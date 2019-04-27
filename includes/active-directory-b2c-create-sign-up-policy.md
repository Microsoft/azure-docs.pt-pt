---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456905"
---
Se quiser permitir que apenas Inscreva-se na sua aplicação, utilize um **Inscreva-se** fluxo de utilizador. Este fluxo de utilizador descreve as experiências que os clientes passam por durante a inscrição e os conteúdos de tokens que a aplicação recebe nas inscrições com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sob **Manage**, selecione **fluxos de utilizador**.

Clique em +**novo fluxo de utilizador** na parte superior do painel.

Sob **selecionar um tipo de fluxo de utilizador**, selecione **todos os**e, em seguida, selecione a versão do **Inscreva-se** que pretende utilizar.

O **nome** determina o nome do fluxo de utilizador de inscrição utilizado pela sua aplicação. Por exemplo, introduza **SiUp**.

Sob **fornecedores de identidade**, selecione **inscrição de E-Mail**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados.

Sob **atributos de utilizador e afirmações**, clique em **mostrar mais**.

Na **atributo recolher** coluna, escolha os atributos que quer recolher do consumidor durante a inscrição. Por exemplo, selecione **País/Região**, **Nome a Apresentar**, e **Código Postal**.

Na **declaração de retorna** coluna, escolher as afirmações que quer que sejam devolvidas nos tokens enviados para a sua aplicação depois de uma experiência de inscrição com êxito. Por exemplo, selecione **Nome a Apresentar**, **Fornecedor de Identidade**, **Código Postal**, **O utilizador é novo** e **ID de Objeto do Utilizador**.

Clique em **OK**.

Clique em **Criar**. O fluxo de utilizador criado aparece como **B2C_1_SiUp** (o **B2C\_1\_**  fragmento é adicionado automaticamente).

Clique em **executar o fluxo de utilizador**.

Selecione **aplicação Contoso B2C** no **aplicativo** menu pendente e `https://localhost:44321/` no **URL de resposta** pendente.

Clique em **executar o fluxo de utilizador**. É aberto um novo separador do browser e pode percorrer a experiência de consumidor de inscrição na sua aplicação.

> [!NOTE]
> Demoram até um minuto para criação de fluxos de utilizador e atualizações em vigor.
>

