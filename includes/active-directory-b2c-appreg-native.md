---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183788"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *nativeapp1*.
1. Para **cliente nativo,** selecione **Sim**.
1. Introduza um **Custom Redirect URI** com um esquema único. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Existem duas considerações importantes na escolha de um URI redirecionado:
    * **Único:** O esquema do URI redirecionado deve ser único para cada aplicação. No `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` exemplo, `com.onmicrosoft.contosob2c.exampleapp` está o esquema. Este padrão deve ser seguido. Se duas aplicações partilharem o mesmo esquema, é dada ao utilizador a opção de escolher uma aplicação. Se o utilizador escolher incorretamente, o inserido falha.
    * **Completo**: O redirecionamento URI deve ter um esquema e um caminho. O caminho deve conter pelo menos um corte para a frente após o domínio. Por exemplo, `//oauth/` funciona enquanto `//oauth` falha. Não inclua personagens especiais no URI, por exemplo, sublinha.
1. Selecione **Criar**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *nativeapp1*.
1. Nos tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional ou qualquer fornecedor**de identidade.
1. Em **Redirect URI,** utilize a queda para selecionar **cliente/nativo público (mobile & desktop)**.
1. Introduza um URI redirecionamento com um esquema único. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Existem duas considerações importantes na escolha de um URI redirecionado:
    * **Único:** O esquema do URI redirecionado deve ser único para cada aplicação. No `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` exemplo, `com.onmicrosoft.contosob2c.exampleapp` está o esquema. Este padrão deve ser seguido. Se duas aplicações partilharem o mesmo esquema, é dada ao utilizador a opção de escolher uma aplicação. Se o utilizador escolher incorretamente, o inserido falha.
    * **Completo**: O redirecionamento URI deve ter um esquema e um caminho. O caminho deve conter pelo menos um corte para a frente após o domínio. Por exemplo, `//oauth/` funciona enquanto `//oauth` falha. Não inclua personagens especiais no URI, por exemplo, sublinha.
1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.