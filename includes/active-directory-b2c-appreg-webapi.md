---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720263"
---
Os recursos da API Web precisam ser registrados em seu locatário antes que possam aceitar e responder a solicitações de recursos protegidos por aplicativos cliente que apresentam um token de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
1. Para **aplicativo Web/API Web**, selecione **Sim**.
1. Para **permitir fluxo implícito**, selecione **Sim**.
1. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:5000`.
1. Para **URI da ID do aplicativo**, adicione um identificador de ponto de extremidade de API ao URI mostrado. Para este tutorial, insira `api`, para que o URI completo seja semelhante a `https://contosob2c.onmicrosoft.com/api`.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** para uso em uma etapa posterior.
