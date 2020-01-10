---
title: Ocultar ou excluir o pacote de acesso no gerenciamento de direitos-Azure AD
description: Saiba como ocultar ou excluir um pacote de acesso no gerenciamento de direitos Azure Active Directory.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422624"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ocultar ou excluir um pacote de acesso no gerenciamento de direitos do Azure AD

Os pacotes do Access são detectáveis por padrão. Isso significa que, se uma política permitir que um usuário solicite o pacote de acesso, ele verá automaticamente o pacote de acesso listado em seu portal meu acesso. No entanto, você pode alterar a configuração **oculta** para que o pacote de acesso não esteja listado no meu portal de acesso do usuário.

Este artigo descreve como ocultar ou excluir um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a configuração oculta

Siga estas etapas para alterar a configuração **oculta** de um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, clique em **Editar**.

1. Defina a configuração **oculta** .

    Se definido como **não**, o pacote de acesso será listado no meu portal de acesso do usuário.

    Se definido como **Sim**, o pacote de acesso não será listado no meu portal de acesso do usuário. A única maneira de um usuário exibir o pacote de acesso é se eles tiverem o **link direto meu acesso do portal** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Excluir um pacote de acesso

Um pacote do Access só poderá ser excluído se não tiver nenhuma atribuição de usuário ativa. Siga estas etapas para excluir um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remova o acesso para todos os usuários.

1. No menu à esquerda, clique em **visão geral** e, em seguida, clique em **excluir**.

1. Na mensagem de exclusão exibida, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
