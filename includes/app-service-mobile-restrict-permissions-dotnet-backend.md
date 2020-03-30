---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184752"
---
Por padrão, as APIs numa extremidade de apps móveis podem ser invocadas anonimamente. Em seguida, você precisa restringir o acesso a apenas clientes autenticados.  

* **Extremidade de lado nonó (através do portal Azure)** :  

    Nas definições das suas Aplicações Móveis, clique em **Tabelas Fáceis** e selecione a sua tabela. Clique em **alterar permissões,** selecione **acesso autenticado apenas** para todas as permissões e, em seguida, clique em **Guardar**.
* **.NET back end (C#)**:  

    No projeto do servidor, navegue para **controladores** > **TodoItemController.cs**. Adicione `[Authorize]` o atributo à classe **TodoItemController,** da seguinte forma. Para restringir o acesso apenas a métodos específicos, também pode aplicar este atributo apenas a esses métodos em vez da classe. Republique o projeto do servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js backend (via Código Node.js)** :  

    Para exigir a autenticação para acesso à mesa, adicione a seguinte linha ao script do servidor Node.js:

        table.access = 'authenticated';

    Para mais detalhes, consulte [Como: Exigir autenticação para acesso às mesas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para aprender a descarregar o projeto de código quickstart a partir do seu site, consulte [Como: Descarregue o projeto de código de arranque rápido Node.js usando Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
