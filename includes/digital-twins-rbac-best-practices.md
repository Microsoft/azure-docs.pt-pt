---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1c8237ce4e8b758395567e939c1ed4bda1f297ff
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827652"
---
O controle de acesso baseado em função é uma estratégia de segurança controlada por herança para gerenciar o acesso, as permissões e as funções. As funções descendentes herdam permissões de funções pai. As permissões também podem ser atribuídas sem serem herdadas de uma função pai. Eles também podem ser atribuídos para personalizar uma função, conforme necessário.

Por exemplo, um administrador de espaço pode precisar de acesso global para executar todas as operações para um espaço especificado. O acesso inclui todos os nós abaixo ou dentro do espaço. Um instalador de dispositivo pode precisar apenas de permissões de *leitura* e *atualização* para dispositivos e sensores.

Em todos os casos, as funções são concedidas *exatamente e não mais do que o acesso necessário* para atender às suas tarefas de acordo com o princípio de privilégio mínimo. De acordo com esse princípio, uma identidade é concedida *apenas*:

* A quantidade de acesso necessária para concluir seu trabalho.
* Uma função apropriada e limitada para realizar seu trabalho.

>[!IMPORTANT]
> Sempre siga o princípio de privilégios mínimos.

Duas outras práticas importantes de controle de acesso baseado em função a seguir:

> [!div class="checklist"]
> * Auditar periodicamente atribuições de função para verificar se cada função tem as permissões corretas.
> * Limpar funções e atribuições quando indivíduos alterarem funções ou atribuições.