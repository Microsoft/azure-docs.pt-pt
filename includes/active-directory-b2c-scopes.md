---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474864"
---
#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Selecione **aplicativos**.
1. Selecione o aplicativo *webapi1* para abrir sua página de **Propriedades** .
1. Selecione **escopos publicados**. Os escopos publicados podem ser usados para conceder a um aplicativo cliente determinadas permissões para a API da Web.
1. Para **escopo**, insira `demo.read`e, para **descrição**, insira `Read access to the web API`.
1. Para **escopo**, insira `demo.write`e, para **descrição**, insira `Write access to the web API`.
1. Selecione **Guardar**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **registros de aplicativo (versão prévia)** .
1. Selecione o aplicativo *webapi1* para abrir sua página de **visão geral** .
1. Em **gerenciar**, selecione **expor uma API**.
1. Ao lado de **ID do aplicativo URI**, selecione o link **definir** .
1. Substitua o valor padrão (um GUID) por `api`e, em seguida, selecione **salvar**. O URI completo é mostrado e deve estar no formato `https://your-tenant-name.onmicrosoft.com/api`. Quando o aplicativo Web solicita um token de acesso para a API, ele deve adicionar esse URI como o prefixo para cada escopo que você definir para a API.
1. Em **escopos definidos por essa API**, selecione **Adicionar um escopo**.
1. Insira os valores a seguir para criar um escopo que define o acesso de leitura à API e, em seguida, selecione **Adicionar escopo**:
    1. **Nome do escopo**: `demo.read`
    1. **Nome de exibição do consentimento do administrador**: `Read access to demo API`
    1. **Descrição do consentimento do administrador**: `Allows read access to the demo API`
1. Selecione **Adicionar um escopo**, insira os valores a seguir para adicionar um escopo que define o acesso de gravação à API e, em seguida, selecione **Adicionar escopo**:
    1. **Nome do escopo**: `demo.write`
    1. **Nome de exibição do consentimento do administrador**: `Write access to demo API`
    1. **Descrição do consentimento do administrador**: `Allows write access to the demo API`