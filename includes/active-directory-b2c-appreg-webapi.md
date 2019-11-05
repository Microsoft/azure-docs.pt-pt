---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: da92c40b051a3dc34e61d056c63f3207d4f42f1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475128"
---
Os recursos da API Web precisam ser registrados em seu locatário antes que possam aceitar e responder a solicitações de recursos protegidos por aplicativos cliente que apresentam um token de acesso.

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a experiência de visualização](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
1. Para **aplicativo Web/API Web**, selecione **Sim**.
1. Para **permitir fluxo implícito**, selecione **Sim**.
1. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:5000`.
1. Para **URI da ID do aplicativo**, adicione um identificador de ponto de extremidade de API ao URI mostrado. Para este tutorial, insira `api`, para que o URI completo seja semelhante ao `https://contosob2c.onmicrosoft.com/api`.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** para uso em uma etapa posterior.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `http://localhost:5000`.
1. Selecione **Registar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, habilite o fluxo de concessão implícita:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .
1. Selecione **Guardar**.