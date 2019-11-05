---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 35ddec2d605e17a1bb91b338e4e5402c6d47db57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474782"
---
Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a experiência de visualização](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Para **cliente nativo**, selecione **Sim**.
1. Deixe os outros valores como estão e, em seguida, selecione **criar**.
1. Registre a **ID do aplicativo** para uso em uma etapa posterior.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *ROPC_Auth_app*.
1. Deixe os outros valores como estão e, em seguida, selecione **registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **tipo de cliente padrão**, selecione **Sim** para tratar o aplicativo como um cliente público. Essa configuração é necessária para o fluxo ROPC.
1. Selecione **Guardar**.