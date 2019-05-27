---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119015"
---
Controlo de acesso baseado em funções é uma estratégia de segurança condicionada por herança para a gestão de acesso, permissões e funções. Funções descendentes herdarem permissões do funções do principal. Permissões também podem ser atribuídas sem a ser herdadas a partir de uma função principal. Também eles podem ser atribuídos a uma função ser personalizados conforme necessário.

Por exemplo, um administrador de espaço poderá ter acesso global para executar todas as operações para um espaço especificados. O acesso inclui todos os nós abaixo ou dentro do espaço de. Um instalador de dispositivo poderá apenas precisar *ler* e *atualizar* permissões para os dispositivos e sensores.

Em todos os casos, as funções é concedida *exatamente e não mais do que o acesso necessário* para atender às suas tarefas pelo princípio do menor privilégio. De acordo com esse princípio, uma identidade é concedida *apenas*:

* A quantidade de acesso necessário para concluir seu trabalho.
* Uma função apropriada e limitada a realização de seu trabalho.

>[!IMPORTANT]
> Seguem sempre o princípio do menor privilégio.

Dois outras importantes de acesso baseado em funções controlo práticas a seguir:

> [!div class="checklist"]
> * Realize auditorias periódicas atribuições de funções para verificar se cada função tem as permissões corretas.
> * Limpe e atribuições de funções quando os indivíduos alterar funções ou atribuições.