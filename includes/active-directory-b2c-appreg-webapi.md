---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183392"
---
Os recursos da Web API precisam de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações de clientes que apresentem um sinal de acesso.

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Insira um nome para a aplicação. Por exemplo, *webapi1*.
1. Para **web app / Web API,** selecione **Sim**.
1. Para **permitir o fluxo implícito,** selecione **Sim**.
1. Para **o URL de resposta,** insira um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Neste tutorial, a amostra corre localmente e ouve `https://localhost:5000`.
1. Para **o ID da aplicação URI,** adicione um identificador de ponto final da API ao URI mostrado. Para este tutorial, insira `api`, para que o URI completo seja semelhante ao `https://contosob2c.onmicrosoft.com/api`.
1. Selecione **Criar**.
1. Grave o ID de **aplicação** para utilização num passo posterior.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *webapi1*.
1. Em **Redirecione o URI**, selecione **Web**e, em seguida, introduza um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Neste tutorial, a amostra corre localmente e ouve `http://localhost:5000`.
1. Selecione **Registar**.
1. Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.

Em seguida, permitir o fluxo implícito de subvenção:

1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a subvenção Implícita,** selecione tanto as **fichas** de acesso como as **fichas de identificação** das caixas de verificação.
1. Selecione **Guardar**.