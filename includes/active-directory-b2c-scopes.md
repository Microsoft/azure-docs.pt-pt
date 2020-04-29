---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183394"
---
#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Selecione **Aplicações**.
1. Selecione a aplicação *webapi1* para abrir a sua página **Propriedades.**
1. Selecione **os âmbitos publicados**. Os âmbitos publicados podem ser utilizados para conceder a uma aplicação de cliente determinadas permissões à Web API.
1. Para o `demo.read` **ÂMBITO,** introduza, `Read access to the web API`e para **DESCRIÇÃO,** introduza .
1. Para o `demo.write` **ÂMBITO,** introduza, `Write access to the web API`e para **DESCRIÇÃO,** introduza .
1. Selecione **Guardar**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione **registos de aplicativos (Pré-visualização)**.
1. Selecione a aplicação *webapi1* para abrir a sua página **de visão geral.**
1. Em **'Gerir',** selecione **Expor uma API**.
1. Ao lado do **ID de aplicação URI,** selecione o link **set.**
1. Substitua o valor predefinido `api`(um GUID) e, em seguida, selecione **Guardar**. O URI completo é mostrado, e `https://your-tenant-name.onmicrosoft.com/api`deve estar no formato . Quando a sua aplicação web solicita um sinal de acesso para a API, deve adicionar este URI como prefixo para cada âmbito que define para a API.
1. Em **Âmbitos definidos por este API,** selecione **Adicionar um âmbito**.
1. Introduza os seguintes valores para criar um âmbito que define o acesso à API e, em seguida, **selecione Adicionar o âmbito:**
    1. **Nome do âmbito:**`demo.read`
    1. Nome de **exibição de consentimento administrativo:**`Read access to demo API`
    1. **Descrição do consentimento do administrador:**`Allows read access to the demo API`
1. **Selecione Adicionar um âmbito,** introduzir os seguintes valores para adicionar um âmbito que define o acesso à API e, em seguida, selecione **Adicionar o âmbito:**
    1. **Nome do âmbito:**`demo.write`
    1. Nome de **exibição de consentimento administrativo:**`Write access to demo API`
    1. **Descrição do consentimento do administrador:**`Allows write access to the demo API`