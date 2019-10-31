---
title: Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como exibir, adicionar e remover atribuições de um pacote de acesso no gerenciamento de direitos Azure Active Directory.
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
ms.openlocfilehash: 3f06da43e758c24da65664b34dd83f876a00e3a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174905"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos do Azure AD

No gerenciamento de direitos do Azure AD, você pode ver quem foi atribuído a pacotes de acesso, sua política e status. Se um pacote do Access tiver uma política apropriada, você também poderá atribuir diretamente o usuário a um pacote do Access. Este artigo descreve como exibir, adicionar e remover atribuições de pacotes do Access.

## <a name="view-who-has-an-assignment"></a>Exibir quem tem uma atribuição

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **atribuições** para ver uma lista de atribuições ativas.

    ![Lista de atribuições para um pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Clique em uma atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tenham todas as funções de recurso provisionadas corretamente, clique no status do filtro e selecione **entrega**.

    Você pode ver detalhes adicionais sobre erros de entrega, localizando a solicitação correspondente do usuário na página **solicitações** .

1. Para ver as atribuições expiradas, clique no status do filtro e selecione **expirado**.

1. Para baixar um arquivo CSV da lista filtrada, clique em **baixar**.

## <a name="directly-assign-a-user"></a>Atribuir um usuário diretamente

Em alguns casos, talvez você queira atribuir diretamente usuários específicos a um pacote do Access para que os usuários não precisem passar pelo processo de solicitar o pacote de acesso. Para atribuir usuários diretamente, o pacote de acesso deve ter uma política que permita atribuições diretas de administrador.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.

1. Clique em **nova atribuição** para abrir Adicionar usuário ao pacote do Access.

    ![Atribuições-Adicionar usuário ao pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Clique em **Adicionar usuários** para selecionar os usuários aos quais você deseja atribuir este pacote de acesso.

1. Na lista **selecionar política** , selecione uma política que o ciclo de vida e as solicitações futuras dos usuários serão governadas e controladas pelo. Se desejar que os usuários selecionados tenham configurações de política diferentes, você poderá clicar em **criar nova política** para adicionar uma nova política.

1. Defina a data e a hora em que você deseja que a atribuição de usuários selecionados inicie e termine. Se uma data de término não for fornecida, as configurações do ciclo de vida da política serão usadas.

1. Opcionalmente, forneça uma justificativa para sua atribuição direta para manutenção de registros.

1. Clique em **Adicionar** para atribuir diretamente os usuários selecionados ao pacote de acesso.

    Depois de alguns instantes, clique em **Atualizar** para ver os usuários na lista atribuições.

## <a name="next-steps"></a>Passos seguintes

- [Solicitações de alteração e configurações de um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
