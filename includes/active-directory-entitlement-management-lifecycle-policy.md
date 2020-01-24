---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694365"
---
## <a name="lifecycle"></a>Ciclo de vida

No separador **Lifecycle,** especifice quando a atribuição de um utilizador ao pacote de acesso expira. Também pode especificar se os utilizadores podem estender as suas atribuições.

1. Na secção **Expiração,** as atribuições de pacotes de **acesso expiram** até à **data**, **Número de dias**, ou **Nunca**.

    Para **data de trabalho**, selecione uma data de validade no futuro.

    Durante **o número de dias**, especifique um número entre 0 e 3660 dias.

    Com base na sua seleção, a atribuição de um utilizador ao pacote de acesso expira numa determinada data, um certo número de dias após a sua aprovação, ou nunca.

1. Clique em **Mostrar definições de expiração avançadas** para mostrar definições adicionais.

    ![Pacote de acesso - Definições de Expiração do Ciclo de Vida](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Para permitir que o utilizador prolongue as suas atribuições, **detete te permita que os utilizadores aumentem o acesso** ao **Sim**.

    Se forem permitidas extensões na apólice, o utilizador receberá um e-mail 14 dias e também 1 dia antes da sua atribuição do pacote de acesso expirar, levando-os a prolongar a atribuição. Se o utilizador apresentar um pedido de extensão do acesso, a data de extensão deve ser em ou antes de as atribuições expirarem conforme definido na política que foi utilizada para conceder ao utilizador o acesso ao pacote de acesso. Por exemplo, se a apólice indicar que as atribuições estão previstas para expirar a 30 de junho, a extensão máxima que um utilizador pode solicitar é 30 de junho.

    Se o acesso de um utilizador for alargado, não poderá solicitar o pacote de acesso após a data de extensão especificada (data definida no fuso horário do utilizador que criou a apólice).

1. Para exigir a aprovação para conceder uma extensão, definido **Exigir aprovação para conceder extensão** a **Sim**.

    Serão utilizadas as mesmas definições de aprovação especificadas no separador **Pedidos.**

1. Clique em **Seguir** ou **atualizar**.
