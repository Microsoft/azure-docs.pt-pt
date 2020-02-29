---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187011"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Insira um nome para a aplicação. Por exemplo, *ROPC_Auth_app.*
1. Para **cliente nativo,** selecione **Sim**.
1. Deixe os outros valores como estão e, em seguida, selecione **Criar**.
1. Grave o ID de **aplicação** para utilização num passo posterior.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *ROPC_Auth_app.*
1. Deixe os outros valores como estão e, em seguida, selecione **Register**.
1. Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.
1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob o tipo de **cliente Predefinido,** selecione **Sim** para tratar a aplicação como cliente público. Esta definição é necessária para o fluxo ROPC.
1. Selecione **Guardar**.