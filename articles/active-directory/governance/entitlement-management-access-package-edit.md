---
title: Ocultar ou apagar pacote de acesso na gestão de direitos - Azure AD
description: Saiba como ocultar ou apagar um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798805"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ocultar ou apagar um pacote de acesso na gestão de direitos Azure AD

Os pacotes de acesso são detetáveis por padrão. Isto significa que, se uma política permitir que um utilizador solicite o pacote de acesso, verá automaticamente o pacote de acesso listado no seu portal My Access. No entanto, pode alterar a definição **Oculta** para que o pacote de acesso não esteja listado no portal My Access do utilizador.

Este artigo descreve como ocultar ou apagar um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a definição oculta

Siga estes passos para alterar a definição **Oculta** para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Na página 'Vista Geral', clique **em Editar**.

1. Defina a definição **Oculta.**

    Se definido para **Nº,** o pacote de acesso será listado no portal My Access do utilizador.

    Se for definido para **Sim,** o pacote de acesso não será listado no portal My Access do utilizador. A única forma de um utilizador visualizar o pacote de acesso é se tiver o link direto do **portal My Access** para o pacote de acesso. Para mais informações, consulte [o Link Share para solicitar um pacote de acesso.](entitlement-management-access-package-settings.md)

## <a name="delete-an-access-package"></a>Excluir um pacote de acesso

Um pacote de acesso só pode ser eliminado se não tiver atribuições ativas do utilizador. Siga estes passos para eliminar um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. No menu esquerdo, clique em **Atribuições** e remova o acesso a todos os utilizadores.

1. No menu esquerdo, clique em **'Overview'** e clique em **Apagar**.

1. Na mensagem de exclusão que aparece, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Ver, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Ver relatórios e registos](entitlement-management-reports.md)
