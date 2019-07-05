---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 9a5b3b04287a8b732d01bd8fe4610e073332da0d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478969"
---
1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel à esquerda e, em seguida, abra a **propriedades** painel. Copie o **ID do diretório** para um ficheiro temporário. Irá utilizar este valor para configurar uma aplicação de exemplo na secção seguinte.

    ![ID de diretório do Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. Na [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel à esquerda e, em seguida, abra a **registos de aplicações (Legado)** painel. Selecione o **novo registo de aplicação** botão.

1. Dê um nome amigável para este registo de aplicações no **nome** caixa. Escolher **tipo de aplicação** como **nativo**, e **URI de redirecionamento** como `https://microsoft.com`. Selecione **Criar**.

    ![Criar Painel](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Abra a aplicação registada e copie o valor do **ID da aplicação** campo num arquivo temporário. Este valor identifica a sua aplicação do Azure Active Directory. Usará o ID da aplicação para configurar o aplicativo de exemplo nas seções a seguir.

    ![ID da aplicação do Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Abra o painel de registo de aplicação. Selecione **configurações** > **permissões obrigatórias**e, em seguida:

   a. Selecione **Add** no canto superior esquerdo para abrir o **adicionar acesso à API** painel.

   b. Selecione **selecionar uma API** e procure **duplos Digital do Azure**. Se a pesquisa não localizar a API, procure **Azure Smart Spaces** como alternativa.

   c. Selecione o **duplos Digital do Azure (serviço do Azure inteligente espaços)** opção e escolha **selecione**.

   d. Escolher **selecionar permissões**. Selecione o **acesso de leitura/escrita** permissões delegadas caixa de verificação e escolha **selecione**.

   e. Selecione **feito** no **adicionar acesso à API** painel.

   f. Na **permissões obrigatórias** painel, selecione a **conceder permissões** botão e aceite a confirmação de que é apresentada. Se a permissão não for concedida para esta API, contacte o administrador.

      ![Painel permissões obrigatórias](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 