---
title: Ocultar ou excluir pacote de acesso na gestão de direitos - Azure AD
description: Aprenda a ocultar ou a eliminar um pacote de acesso na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376595"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ocultar ou eliminar um pacote de acesso na gestão de direitos da AD Azure

Os pacotes de acesso são detetáveis por defeito. Isto significa que, se uma apólice permitir que um utilizador solicite o pacote de acesso, verá automaticamente o pacote de acesso listado no seu portal My Access. No entanto, pode alterar a definição **Oculta** para que o pacote de acesso não esteja listado no portal My Access do utilizador.

Este artigo descreve como esconder ou apagar um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a definição oculta

Siga estes passos para alterar a definição **oculta** para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página 'Overview', clique em **Editar**.

1. Defina a definição **escondida.**

    Se definido para **Não,** o pacote de acesso será listado no portal My Access do utilizador.

    Se definido para **Sim,** o pacote de acesso não será listado no portal My Access do utilizador. A única forma de um utilizador ver o pacote de acesso é se tiver o link do **portal My Access** direto para o pacote de acesso. Para mais informações, consulte o [link Share para solicitar um pacote](entitlement-management-access-package-settings.md)de acesso .

## <a name="delete-an-access-package"></a>Eliminar um pacote de acesso

Um pacote de acesso só pode ser eliminado se não tiver atribuições de utilizador ativos. Siga estes passos para eliminar um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu esquerdo, clique em **Atribuições** e remova o acesso a todos os utilizadores.

1. No menu esquerdo, clique em **Visualização geral** e, em seguida, clique em **Apagar**.

1. Na mensagem de exclusão que aparece, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Ver, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Ver relatórios e registos](entitlement-management-reports.md)
