---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92379728"
---
- Cada identidade gerida conta para o limite de quota de objeto num inquilino AD Azure, conforme descrito nos [limites e restrições de serviço Azure AD](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   A taxa a que podem ser criadas identidades geridas tem os seguintes limites:

    1. Per Azure AD Inquilino por Região de Azure: 200 criam operações por 20 segundos.
    2. Por Azure Subscrição por Região de Azure: 40 criam operações por 20 segundos.

- Quando cria identidades geridas atribuídas pelo utilizador, apenas os caracteres alfanuméricos (0-9, a-z e A-Z) e o hífen (-) são suportados. Para a atribuição a uma máquina virtual ou à escala de máquina virtual definida para funcionar corretamente, o nome está limitado a 24 caracteres.
