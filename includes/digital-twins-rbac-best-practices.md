---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044955"
---
O controlo de acesso baseado em papéis é uma estratégia de segurança orientada para a gestão do acesso, permissões e funções. Os papéis descendentes herdam permissões dos papéis dos pais. As permissões também podem ser atribuídas sem serem herdadas de um papel-mãe. Também podem ser designados para personalizar um papel conforme necessário.

Por exemplo, um Administrador Espacial pode precisar de acesso global para executar todas as operações para um espaço especificado. O acesso inclui todos os nós por baixo ou dentro do espaço. Um instalador de dispositivos pode necessitar apenas de *ser lido* e *atualizado* permissões para dispositivos e sensores.

Em todos os casos, as funções são concedidas *exatamente e não mais do que o acesso necessário* para cumprir as suas tarefas de acordo com o Princípio do Menor Privilégio. De acordo com este princípio, só é *concedida*uma identidade:

* A quantidade de acesso necessária para completar o seu trabalho.
* Um papel adequado e limitado ao seu trabalho.

>[!IMPORTANT]
> Siga sempre o Princípio do Menos Privilégio.

Duas outras práticas importantes de controlo de acesso baseadas em papéis a seguir:

> [!div class="checklist"]
> * Audite periodicamente atribuições de funções para verificar se cada função tem as permissões corretas.
> * Limpe funções e atribuições quando os indivíduos mudam de funções ou atribuições.