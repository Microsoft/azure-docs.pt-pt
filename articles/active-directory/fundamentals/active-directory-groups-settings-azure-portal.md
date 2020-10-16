---
title: Edite as informações do seu grupo - Azure Ative Directory / Microsoft Docs
description: Instruções sobre como editar as informações do seu grupo utilizando o Diretório Ativo Azure.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e9b38208526ba83e0a0e31e2e139e340a3ee511
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797530"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Edite as informações do seu grupo utilizando o Azure Ative Directory

Utilizando o Azure Ative Directory (Azure AD), pode editar as definições de um grupo, incluindo atualizar o seu nome, descrição ou tipo de membro.

## <a name="to-edit-your-group-settings"></a>Para editar as definições de grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**e, em seguida, selecione **Grupos**.

    Os **Grupos - Todas as** páginas de grupos aparecem, mostrando todos os seus grupos ativos.

3. A partir da página **Grupos - Todos os grupos,** digite o máximo de nome de grupo possível na caixa **de Pesquisa.** Para efeitos deste artigo, estamos à procura da política do MDM- Grupo **Ocidental.**

    Os resultados da pesquisa aparecem na caixa **de Pesquisa,** atualizando à medida que digita mais caracteres.

    ![Página de todos os grupos, com texto de pesquisa na caixa de pesquisa](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecione a **política de MDM**do grupo - Oeste , e, em seguida, selecione **Propriedades** da área **De gestão.**

    ![Página geral do grupo, com opção de membro e informação em destaque](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Atualizar as **informações gerais de definições** conforme necessário, incluindo:

    ![Definições de propriedades para um grupo](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nome de grupo.** Editar o nome de grupo existente.
    
    - **Descrição do grupo.** Editar a descrição do grupo existente.

    - **Tipo de grupo.** Não se pode mudar o tipo de grupo depois de ter sido criado. Para alterar o **tipo de grupo,** tem de eliminar o grupo e criar um novo.
    
    - **Tipo de associação.** Alterar o tipo de membro. Para obter mais informações sobre os vários tipos de membros disponíveis, consulte [Como: Criar um grupo básico e adicionar membros utilizando o portal Azure Ative Directory](active-directory-groups-create-azure-portal.md).
    
    - **Identificação de objeto.** Não é possível alterar o ID do objeto, mas pode copiá-lo para ser utilizado nos comandos PowerShell para o grupo. Para obter mais informações sobre a utilização de cmdlets PowerShell, consulte [cmdlets do Azure Ative Directory para configurar as definições do grupo](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Como adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Gerir associações de um grupo](active-directory-groups-membership-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Associar ou adicionar uma assinatura Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md)
