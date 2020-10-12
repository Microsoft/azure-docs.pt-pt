---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694365"
---
## <a name="lifecycle"></a>Ciclo de vida

No **separador Ciclo de Vida,** especifique quando expira a atribuição de um utilizador ao pacote de acesso. Também pode especificar se os utilizadores podem estender as suas atribuições.

1. Na secção **expiração,** as **atribuições de pacotes de acesso terminam** até à **data**, **Número de dias**, ou **Nunca**.

    Para **a data,** selecione uma data de validade no futuro.

    Para **o número de dias**, especifique um número entre 0 e 3660 dias.

    Com base na sua seleção, a atribuição de um utilizador ao pacote de acesso expira numa determinada data, um certo número de dias após a sua aprovação, ou nunca.

1. Clique **em Mostrar Definições de expiração avançadas** para mostrar definições adicionais.

    ![Pacote de acesso - Definições de expiração do ciclo de vida](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Para permitir que o utilizador aumente as suas atribuições, desateia-se **permitir que os utilizadores alargassem** o acesso ao **Sim.**

    Se as extensões forem permitidas na apólice, o utilizador receberá um e-mail 14 dias e também 1 dia antes da sua atribuição do pacote de acesso estar definida para expirar, levando-os a prolongar a atribuição. Se o utilizador submeter um pedido de alargamento do acesso, a data de extensão deve ser ou antes de as atribuições expirarem conforme definido na política que foi usada para conceder ao utilizador acesso ao pacote de acesso. Por exemplo, se a apólice indicar que as atribuições estão definidas para expirar no dia 30 de junho, a extensão máxima que um utilizador pode solicitar é 30 de junho.

    Se o acesso de um utilizador for prolongado, não poderá solicitar o pacote de acesso após a data de extensão especificada (data definida no fuso horário do utilizador que criou a apólice).

1. Para exigir aprovação para conceder uma extensão, **detete-se a aprovação para conceder a extensão** ao **Sim**.

    Serão utilizadas as mesmas definições de aprovação especificadas no separador **Pedidos.**

1. Clique **em Seguinte** ou **Atualização**.
