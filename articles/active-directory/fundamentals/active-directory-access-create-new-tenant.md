---
title: Início rápido-acesso & criar novo locatário-Azure AD
description: Instruções sobre como localizar o Azure Active Directory e como criar um novo inquilino para a sua organização.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef577f5d086b22779392cd6d8b5d43a340f7329
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423106"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Início rápido: Criar um novo inquilino no Azure Active Directory
Pode fazer todas as suas tarefas administrativas com o portal do Microsoft Azure Active Directory (Microsoft Azure AD), incluindo a criação de um novo inquilino para a sua organização. 

Neste início rápido, ficará a saber como aceder ao portal do Azure e do Microsoft Azure Active Directory e aprenderá como criar um inquilino básico para a sua organização.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-new-tenant-for-your-organization"></a>Criar um novo inquilino para a sua organização
Depois de iniciar sessão no portal do Azure, pode criar um novo inquilino para a sua organização. O novo inquilino representa a sua organização e ajuda-o a gerir uma instância específica dos serviços cloud da Microsoft para os seus utilizadores internos e externos.

### <a name="to-create-a-new-tenant"></a>Para criar um novo inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) da sua organização com uma conta de Administrador global.

1. No menu portal do Azure, selecione **criar um recurso**.  

    ![Página Azure Active Directory criar Resource](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Selecione **identidade**e, em seguida, selecione **Azure Active Directory**.

    É apresentada a página **Criar diretório**.

    ![Criar página do Microsoft Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na página **Criar diretório**, introduza as seguintes informações:
    
    - Introduza _Contoso_ na caixa **Nome da organização**.

    - Introduza _Contoso_ na caixa **Nome de domínio inicial**.

    - Deixe a opção _Estados Unidos_ na caixa **País ou região**.

1. Selecione **Criar**.

O novo inquilino é criado com o domínio contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Limpar recursos
Se não quiser continuar a utilizar esta aplicação, poderá eliminar o inquilino através dos seguintes passos:

- Selecione **Azure Active Directory** e, em seguida, na página **Contoso – Descrição Geral**, selecione **Eliminar diretório**.

    O inquilino e a informação associada são eliminados.

    ![Página de visão geral, com o botão excluir diretório realçado](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Passos seguintes
- Para alterar ou adicionar nomes de domínio, veja [Como adicionar um nome de domínio personalizado ao Microsoft Azure Active Directory](add-custom-domain.md)

- Para adicionar utilizadores, veja [Adicionar ou eliminar um novo utilizador](add-users-azure-active-directory.md)

- Para adicionar grupos e membros, veja [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- Saiba mais sobre o [acesso baseado em função usando Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) e [acesso condicional](../../role-based-access-control/conditional-access-azure-management.md) para ajudar a gerenciar o acesso a aplicativos e recursos da sua organização.

- Saiba mais sobre o Azure AD, incluindo [informações básicas de licenciamento, terminologia e funcionalidades associadas](active-directory-whatis.md).
