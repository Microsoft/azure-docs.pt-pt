---
title: Quickstart - Access & criar novo inquilino - Azure AD
description: Instruções sobre como encontrar o Azure Ative Directory e como criar um novo inquilino para a sua organização.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d6341aeb6db89d43ef887a3ae50c4439e3867e6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318612"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Quickstart: Criar um novo inquilino no Azure Ative Directory
Pode fazer todas as suas tarefas administrativas com o portal do Microsoft Azure Active Directory (Microsoft Azure AD), incluindo a criação de um novo inquilino para a sua organização. 

Neste início rápido, ficará a saber como aceder ao portal do Azure e do Microsoft Azure Active Directory e aprenderá como criar um inquilino básico para a sua organização.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-new-tenant-for-your-organization"></a>Criar um novo inquilino para a sua organização
Depois de iniciar sessão no portal do Azure, pode criar um novo inquilino para a sua organização. O novo inquilino representa a sua organização e ajuda-o a gerir uma instância específica dos serviços cloud da Microsoft para os seus utilizadores internos e externos.

### <a name="to-create-a-new-tenant"></a>Para criar um novo inquilino

1. Inscreva-se no [portal Azure](https://portal.azure.com/)da sua organização.

1. No menu do portal do Azure, selecione **Criar um recurso**.  

    ![Diretório Ativo Azure Criar página ressoure](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Selecione **Identidade**e, em seguida, selecione **Azure Ative Directory**.

    É apresentada a página **Criar diretório**.

    ![Criar página do Microsoft Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na página **Criar diretório**, introduza as seguintes informações:
    
    - Introduza _Contoso_ na caixa **Nome da organização**.

    - Introduza _Contoso_ na caixa **Nome de domínio inicial**.

    - Deixe a opção _Estados Unidos_ na caixa **País ou região**.

1. Selecione **Criar**.

O novo inquilino é criado com o domínio contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Limpar os recursos
Se não continuar a utilizar esta aplicação, pode eliminar o arrendatário utilizando os seguintes passos:

- Certifique-se de que está inscrito no diretório que pretende eliminar através do filtro **de subscrição Do Diretório +** no Portal Azure e que, se necessário, muda para o directório-alvo.
- Selecione **Azure Active Directory** e, em seguida, na página **Contoso – Descrição Geral**, selecione **Eliminar diretório**.

    O inquilino e a informação associada são eliminados.

    ![Página geral, com botão de diretório de delete realçado](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Próximos passos
- Para alterar ou adicionar nomes de domínio, veja [Como adicionar um nome de domínio personalizado ao Microsoft Azure Active Directory](add-custom-domain.md)

- Para adicionar utilizadores, veja [Adicionar ou eliminar um novo utilizador](add-users-azure-active-directory.md)

- Para adicionar grupos e membros, veja [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- Saiba mais sobre [o acesso baseado em funções utilizando a Gestão de Identidade Privilegiada](../../role-based-access-control/best-practices.md) e o Acesso [Condicional](../../role-based-access-control/conditional-access-azure-management.md) para ajudar a gerir a aplicação da sua organização e o acesso a recursos.

- Saiba mais sobre o Azure AD, incluindo [informações básicas de licenciamento, terminologia e funcionalidades associadas](active-directory-whatis.md).