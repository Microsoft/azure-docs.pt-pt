---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521267"
---
Para visualizar e copiar as chaves de acesso à sua conta de armazenamento ou a linha de ligação do portal Azure:

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Localize a sua conta de armazenamento.
3. Em **Definições**, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Chave** em **key1**e clique no botão **Copiar** para copiar a chave da conta.
5. Alternadamente, pode copiar toda a cadeia de ligação. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação.

    ![Screenshot mostrando como ver chaves de acesso no portal Azure](media/storage-view-keys-include/portal-connection-string.png)

Pode utilizar qualquer uma das teclas para aceder ao Armazenamento Azure, mas em geral é uma boa prática usar a primeira tecla e reservar a utilização da segunda tecla para quando estiver a rodar chaves.

Para visualizar ou ler as chaves de acesso de uma conta, o utilizador deve ser um Administrador de Serviço, ou deve ser atribuído uma função RBAC que inclua a **Microsoft.Storage/storageAccounts/listkeys/action**. Algumas funções de RBAC incorporadas que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e Serviço de Serviço chave de conta de **armazenamento.** Para obter mais informações sobre o papel de Administrador de Serviço, consulte funções de administrador de [subscrição Classic, funções Azure RBAC e funções Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções incorporadas para o Armazenamento Azure, consulte a secção de **armazenamento** em [papel azure incorporado para o Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage)
