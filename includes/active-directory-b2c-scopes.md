---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bb004afe6cdd6992f742877318426052d1bead6d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073007"
---
#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/) 

1. Selecione **Registos de aplicações**.
1. Selecione a aplicação *webapi1* para abrir a sua página **de Visão Geral.**
1. Em **Gestão**, **selecione Expor uma API**.
1. Ao lado **do ID URI da aplicação,** selecione o link **set.**
1. Substitua o valor predefinido (a GUID) com `api` , e, em seguida, selecione **Guardar**. O URI completo é mostrado, e deve estar no formato `https://your-tenant-name.onmicrosoft.com/api` . Quando a sua aplicação web solicita um token de acesso para a API, deve adicionar este URI como o prefixo para cada âmbito que define para a API.
1. Nos **Âmbitos definidos por esta API,** selecione **Adicionar um âmbito**.
1. Introduza os seguintes valores para criar um âmbito que define o acesso de leitura à API e, em seguida, selecione **adicionar âmbito**:
    1. **Nome do âmbito:**`demo.read`
    1. **Nome do exposição de consentimento de administração:**`Read access to demo API`
    1. **Descrição do consentimento da administração**: `Allows read access to the demo API`
1. **Selecione Adicionar um âmbito,** introduzir os seguintes valores para adicionar um âmbito que define o acesso de escrita à API e, em seguida, selecione **adicionar âmbito**:
    1. **Nome do âmbito:**`demo.write`
    1. **Nome do exposição de consentimento de administração:**`Write access to demo API`
    1. **Descrição do consentimento da administração**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Selecione **Aplicações (Legado)**.
1. Selecione a aplicação *webapi1* para abrir a sua página **Propriedades.**
1. Selecione **âmbitos publicados**. Os âmbitos publicados podem ser utilizados para conceder a um pedido de cliente determinadas permissões à API web.
1. Para **ÂMBITO,** insira `demo.read` , e para **DESCRIÇÃO,** insira `Read access to the web API` .
1. Para **ÂMBITO,** insira `demo.write` , e para **DESCRIÇÃO,** insira `Write access to the web API` .
1. Selecione **Guardar**.