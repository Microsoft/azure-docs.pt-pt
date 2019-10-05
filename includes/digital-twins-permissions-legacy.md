---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.custom: include file
ms.openlocfilehash: 690ff402c632bfc28f1a9a218677e9772f5a3200
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949918"
---
1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **Propriedades** . Copie o **ID do diretório** para um ficheiro temporário. Você usará esse valor para configurar um aplicativo de exemplo na próxima seção.

    [ID do diretório de Active Directory de @no__t 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **registros de aplicativo (Herdado)** . Selecione o botão **novo registro de aplicativo** .

1. Dê um nome amigável para esse registro de aplicativo na caixa **nome** . Escolha o **tipo de aplicativo** como **nativo**e o **URI de redirecionamento** como `https://microsoft.com`. Selecione **Criar**.

    [painel ![Create](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Abra o aplicativo registrado e copie o valor do campo **ID do aplicativo** para um arquivo temporário. Esse valor identifica seu aplicativo Azure Active Directory. Você usará a ID do aplicativo para configurar o aplicativo de exemplo nas seções a seguir.

    [![Azure Active Directory ID do aplicativo](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Abra o painel de registro do aplicativo. Selecione **configurações** > **permissões necessárias**e, em seguida:

   a. Selecione **Adicionar** na parte superior esquerda para abrir o painel **Adicionar acesso à API** .

   b. Selecione **selecionar uma API** e pesquise **gêmeos do Azure digital**. Se a pesquisa não localizar a API, procure **Azure Smart Spaces** como alternativa.

   c. Selecione a opção **gêmeos digital do Azure (serviço de espaços inteligentes do Azure)** e escolha **selecionar**.

   d. Escolha **selecionar permissões**. Marque a caixa de seleção **acesso de leitura/gravação** permissões delegadas e escolha **selecionar**.

   e. Selecione **concluído** no painel **Adicionar acesso à API** .

   f. No painel **permissões necessárias** , selecione o botão **conceder permissões** e aceite a confirmação que aparece. Se a permissão não for concedida para essa API, contate o administrador.

      [painel de permissões ![Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
