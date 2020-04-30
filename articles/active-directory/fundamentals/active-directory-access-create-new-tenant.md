---
title: Quickstart - Acesso & criar novo inquilino - Azure AD
description: Instruções sobre como encontrar o Azure Ative Directory e como criar um novo inquilino para a sua organização.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80049996"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Quickstart: Criar um novo inquilino no Diretório Ativo azure
Pode fazer todas as suas tarefas administrativas com o portal do Microsoft Azure Active Directory (Microsoft Azure AD), incluindo a criação de um novo inquilino para a sua organização. 

Neste início rápido, ficará a saber como aceder ao portal do Azure e do Microsoft Azure Active Directory e aprenderá como criar um inquilino básico para a sua organização.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-new-tenant-for-your-organization"></a>Criar um novo inquilino para a sua organização
Depois de iniciar sessão no portal do Azure, pode criar um novo inquilino para a sua organização. O novo inquilino representa a sua organização e ajuda-o a gerir uma instância específica dos serviços cloud da Microsoft para os seus utilizadores internos e externos.

### <a name="to-create-a-new-tenant"></a>Para criar um novo inquilino

1. Inscreva-se no [portal Azure](https://portal.azure.com/)da sua organização.

1. A partir do menu do portal Azure, selecione **Criar um recurso**.  

    ![Diretório Ativo Azure Criar página de resoure](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Selecione **Identidade**, e, em seguida, selecione **Azure Ative Directory**.

    É apresentada a página **Criar diretório**.

    ![Criar página do Microsoft Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na página **Criar diretório**, introduza as seguintes informações:
    
    - Introduza _Contoso_ na caixa **Nome da organização**.

    - Introduza _Contoso_ na caixa **Nome de domínio inicial**.

    - Deixe a opção _Estados Unidos_ na caixa **País ou região**.

1. Selecione **Criar**.

O novo inquilino é criado com o domínio contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Limpar recursos
Se não vai continuar a utilizar esta aplicação, pode eliminar o inquilino usando os seguintes passos:

- Certifique-se de que está inscrito no diretório que pretende eliminar através do filtro de **subscrição Diretório +** no Portal Azure e de mudar para o directório-alvo, se necessário.
- Selecione **Azure Active Directory** e, em seguida, na página **Contoso – Descrição Geral**, selecione **Eliminar diretório**.

    O inquilino e a informação associada são eliminados.

    ![Página de visão geral, com botão de diretório Delete destacado](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Passos seguintes
- Para alterar ou adicionar nomes de domínio, veja [Como adicionar um nome de domínio personalizado ao Microsoft Azure Active Directory](add-custom-domain.md)

- Para adicionar utilizadores, veja [Adicionar ou eliminar um novo utilizador](add-users-azure-active-directory.md)

- Para adicionar grupos e membros, veja [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- Saiba mais sobre [o acesso baseado em papéis usando a Gestão de Identidade Privilegiada](../../role-based-access-control/pim-azure-resource.md) e acesso [condicional](../../role-based-access-control/conditional-access-azure-management.md) para ajudar a gerir a aplicação da sua organização e o acesso a recursos.

- Saiba mais sobre o Azure AD, incluindo [informações básicas de licenciamento, terminologia e funcionalidades associadas](active-directory-whatis.md).
