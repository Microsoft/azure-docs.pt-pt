---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971044"
---
- Cada identidade gerida conta para o limite de quota de objeto num inquilino AD Azure, conforme descrito nos [limites e restrições de serviço Azure AD](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   A taxa a que podem ser criadas identidades geridas tem os seguintes limites:

    1. Per Azure AD Inquilino por Região de Azure: 200 criam operações por 20 segundos.
    2. Por Azure Subscrição por Região de Azure: 40 criam operações por 20 segundos.

- Quando cria identidades geridas atribuídas pelo utilizador, apenas os caracteres alfanuméricos (0-9, a-z e A-Z) e o hífen (-) são suportados. Para a atribuição a uma máquina virtual ou à escala de máquina virtual definida para funcionar corretamente, o nome está limitado a 24 caracteres.
