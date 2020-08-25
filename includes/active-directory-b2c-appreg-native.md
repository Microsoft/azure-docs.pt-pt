---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "84793711"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregtraining)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *a nativeapp1*.
1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional ou qualquer fornecedor de identidade.**
1. Em **Redirecionar URI,** utilize o drop-down para selecionar **cliente público/nativo (ambiente de trabalho móvel &)**.
1. Introduza um URI de redirecionamento com um esquema único. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Existem considerações importantes na escolha de um URI redirecionado:
    * **Desenvolvimento** Para uso de desenvolvimento, você pode definir o redirecionamento URI para `http://localhost` e Azure AD B2C respeitará qualquer porta no pedido. Se o URI registado contiver uma porta, o Azure AD B2C utilizará apenas essa porta. Por exemplo, se o reorientar uri registado `http://localhost` for, o URI de redirecionamento no pedido pode ser `http://localhost:<randomport>` . Se o reorientar uri registado `http://localhost:8080` for, o URI de redirecionamento no pedido deve ser `http://localhost:8080` .
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser único para cada aplicação. No `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` exemplo, `com.onmicrosoft.contosob2c.exampleapp` está o esquema. Este padrão deve ser seguido. Se duas aplicações partilham o mesmo esquema, o utilizador tem a opção de escolher uma aplicação. Se o utilizador escolher incorretamente, a entrada falha.
    * **Completo**: O URI redirecionado deve ter um esquema e um caminho. O caminho deve conter pelo menos um corte para a frente após o domínio. Por exemplo, `//oauth/` funciona enquanto `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinha.
1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
2. Selecione **Registar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *a nativeapp1*.
1. Para **cliente nativo,** selecione **Sim**.
1. Insira um **URI de redirecionamento personalizado** com um esquema único. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Existem duas considerações importantes na escolha de um URI redirecionado:
    * **Exclusivo**: O esquema do URI de redirecionamento deve ser único para cada aplicação. No `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` exemplo, `com.onmicrosoft.contosob2c.exampleapp` está o esquema. Este padrão deve ser seguido. Se duas aplicações partilham o mesmo esquema, o utilizador tem a opção de escolher uma aplicação. Se o utilizador escolher incorretamente, a entrada falha.
    * **Completo**: O URI redirecionado deve ter um esquema e um caminho. O caminho deve conter pelo menos um corte para a frente após o domínio. Por exemplo, `//oauth/` funciona enquanto `//oauth` falha. Não inclua caracteres especiais no URI, por exemplo, sublinha.
1. Selecione **Criar**.