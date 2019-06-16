---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66139847"
---
Por predefinição, as APIs num back-end de aplicações móveis podem ser invocadas anonimamente. Em seguida, precisa restringir o acesso a apenas os clientes autenticados.  

* **NODE. js volta terminar (através do portal do Azure)** :  

    Nas suas definições de aplicações móveis, clique em **tabelas simples** e selecione a sua tabela. Clique em **alterar permissões**, selecione **apenas acesso autenticado** todas as permissões, e clique em **guardar**.
* **.NET back-end (C#)** :  

    No projeto de servidor, navegue até **controladores** > **TodoItemController.cs**. Adicionar a `[Authorize]` atributo para o **TodoItemController** classe, da seguinte forma. Para restringir o acesso apenas aos métodos específicos, também pode aplicar esse atributo apenas para esses métodos em vez da classe. Voltar a publicar o projeto de servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end de node. js (por meio de código node. js)** :  

    Para exigir autenticação para acesso a tabelas, adicione a seguinte linha no script de servidor node. js:

        table.access = 'authenticated';

    Para obter mais detalhes, consulte [como: Exigir autenticação para o acesso às tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como transferir o projeto de código de início rápido do seu site, consulte [como: Transfira o projeto de código do guia de introdução do node. js back-end com o Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
