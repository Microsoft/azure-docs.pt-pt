---
title: Editar informações do seu grupo - Diretório Ativo Azure / Microsoft Docs
description: Instruções sobre como editar as informações do seu grupo utilizando o Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561887"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Editar informações do seu grupo usando o Diretório Ativo Azure

Utilizando o Azure Ative Directory (Azure AD), pode editar as definições de um grupo, incluindo atualizar o seu nome, descrição ou tipo de membro.

## <a name="to-edit-your-group-settings"></a>Para editar as definições do seu grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Grupos**.

    A página **de Grupos - Todos os grupos** aparecem, mostrando todos os seus grupos ativos.

3. A partir da página **Grupos - Todos os grupos,** digite o máximo de nome de grupo possível na caixa **de pesquisa.** Para efeitos deste artigo, estamos à procura da política do **MDM- Grupo Ocidental.**

    Os resultados da pesquisa aparecem sob a caixa **de pesquisa,** atualizando à medida que escreve mais caracteres.

    ![Página de todos os grupos, com texto de pesquisa na caixa de pesquisa](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecione a **política do grupo MDM - West,** e, em seguida, selecione **Propriedades** da área **De Gerir.**

    ![Página geral do grupo, com opção do membro e informação em destaque](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Atualizar as informações de **definições gerais** conforme necessário, incluindo:

    ![Configurações de propriedades para um grupo](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nome de grupo.** Editar o nome de grupo existente.
    
    - **Descrição do grupo.** Editar a descrição do grupo existente.

    - **Tipo de grupo.** Não se pode mudar o tipo de grupo depois de ter sido criado. Para alterar o **tipo de Grupo,** tem de eliminar o grupo e criar um novo.
    
    - **Tipo de membro.** Mude o tipo de membro. Para obter mais informações sobre os vários tipos de membros disponíveis, consulte [Como: Criar um grupo básico e adicionar membros usando o portal azure Ative Diretório](active-directory-groups-create-azure-portal.md).
    
    - **Identificação do objeto.** Não pode alterar o ID do Objeto, mas pode copiá-lo para usar nos comandos PowerShell para o grupo. Para obter mais informações sobre a utilização de cmdlets PowerShell, consulte os [cmdlets de Diretório Ativo Azure para configurar as definições](../users-groups-roles/groups-settings-v2-cmdlets.md)do grupo .

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Como adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Gerir associações de um grupo](active-directory-groups-membership-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Associar ou adicionar uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md)
