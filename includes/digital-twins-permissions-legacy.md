---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: ef6b395aeff18a63f52f58e2477679b48a19b002
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624751"
---
1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **Propriedades** . Copie o **ID do diretório** para um ficheiro temporário. Você usará esse valor para configurar um aplicativo de exemplo na próxima seção.

    ![ID do diretório Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **registros de aplicativo (Herdado)** . Selecione o botão **novo registro de aplicativo** .

1. Dê um nome amigável para esse registro de aplicativo na caixa **nome** . Escolha o **tipo de aplicativo** como **nativo**e URI de `https://microsoft.com`redirecionamento como. Selecione **Criar**.

    ![Criar painel](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Abra o aplicativo registrado e copie o valor do campo **ID do aplicativo** para um arquivo temporário. Esse valor identifica seu aplicativo Azure Active Directory. Você usará a ID do aplicativo para configurar o aplicativo de exemplo nas seções a seguir.

    ![ID do aplicativo Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Abra o painel de registro do aplicativo. Selecione **configurações** > **permissões necessárias**e, em seguida:

   a. Selecione **Adicionar** na parte superior esquerda para abrir o painel **Adicionar acesso à API** .

   b. Selecione **selecionar uma API** e pesquise **gêmeos do Azure digital**. Se a pesquisa não localizar a API, procure **Azure Smart Spaces** como alternativa.

   c. Selecione a opção **gêmeos digital do Azure (serviço de espaços inteligentes do Azure)** e escolha **selecionar**.

   d. Escolha **selecionar permissões**. Marque a caixa de seleção **acesso de leitura/gravação** permissões delegadas e escolha **selecionar**.

   e. Selecione **concluído** no painel **Adicionar acesso à API** .

   f. No painel **permissões necessárias** , selecione o botão **conceder permissões** e aceite a confirmação que aparece. Se a permissão não for concedida para essa API, contate o administrador.

      ![Painel de permissões necessárias](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 
