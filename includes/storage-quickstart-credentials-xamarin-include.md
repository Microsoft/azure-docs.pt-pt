---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006238"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

Quando o pedido de amostrafaz um pedido ao Armazenamento Azure, deve ser autorizado. Para autorizar um pedido, adicione as credenciais da sua conta de armazenamento ao pedido como uma cadeia de ligação. Veja as credenciais da conta de armazenamento através dos seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, pode ver as chaves de acesso da conta e a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e selecione o botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Depois de ter copiado a sua cadeia de ligação, coloque-a numa variável de nível de classe no seu ficheiro *MainPage.xaml.cs.* Abra *MainPaage.xaml.cs* e `storageConnectionString` encontre a variável. Substitua-a `<yourconnectionstring>` com a sua corda de ligação real.

Aqui está o código:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```