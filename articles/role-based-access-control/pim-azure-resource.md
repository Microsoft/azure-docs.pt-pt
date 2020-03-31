---
title: Gerir acesso aos recursos azure com Azure AD e PIM
description: Saiba gerir o acesso aos recursos azure utilizando a Azure Ative Directory Privileged Identity Management (PIM) e o controlo de acesso baseado em papéis (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138040"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Gerir o acesso aos recursos azure com a Azure AD Privileged Identity Management

Para proteger contas privilegiadas de ciberataques maliciosos, pode utilizar a Azure Ative Directory Privileged Identity Management (PIM) para diminuir o tempo de exposição dos privilégios e aumentar a sua visibilidade para o seu uso através de relatórios e alertas. A PIM fá-lo limitando os utilizadores a assumirem apenas os seus privilégios "mesmo a tempo" (JIT), ou atribuindo privilégios por uma duração reduzida após o qual os privilégios são automaticamente revogados. 

Agora pode utilizar pim com controlo de acesso baseado em funções Azure (RBAC) para gerir, controlar e monitorizar o acesso aos recursos azure. A PIM pode gerir a adesão a funções incorporadas e personalizadas para o ajudar: 

- Permitir o acesso a pedido, "mesmo a tempo" aos recursos do Azure
- Expire o acesso aos recursos automaticamente para utilizadores e grupos atribuídos
- Atribuir acesso temporário aos recursos do Azure para tarefas rápidas ou horários de permanência
- Receba alertas quando novos utilizadores ou grupos forem atribuídos ao acesso aos recursos e quando ativarem atribuições elegíveis

Para mais informações, consulte o que é a [Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)