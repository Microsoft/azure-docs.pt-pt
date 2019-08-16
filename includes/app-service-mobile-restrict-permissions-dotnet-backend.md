---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "67184752"
---
Por padrão, as APIs em um back-end de aplicativos móveis podem ser invocadas anonimamente. Em seguida, você precisa restringir o acesso somente a clientes autenticados.  

* **Back-end do node. js (por meio do portal do Azure)** :  

    Em suas configurações de aplicativos móveis, clique em **tabelas fáceis** e selecione sua tabela. Clique em **alterar permissões**, selecione **acesso autenticado apenas** para todas as permissões e, em seguida, clique em **salvar**.
* **Back-end doC#.net ()** :  

    No projeto do servidor, navegue até **controladores** > **TodoItemController.cs**. Adicione o `[Authorize]` atributo à classe **TodoItemController** , da seguinte maneira. Para restringir o acesso somente a métodos específicos, você também pode aplicar esse atributo apenas a esses métodos em vez da classe. Republique o projeto do servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end do node. js (por meio do código node. js)** :  

    Para exigir autenticação para acesso à tabela, adicione a seguinte linha ao script do servidor node. js:

        table.access = 'authenticated';

    Para obter mais detalhes, [consulte Como: Exigir autenticação para acesso a tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como baixar o projeto de código de início rápido do seu site [, consulte Como: Baixe o projeto de código de início rápido do back-](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)end do node. js usando o git.
