---
title: Adicione ou remova um grupo de outro grupo - Azure AD
description: Instruções sobre como adicionar ou remover um grupo de outro grupo utilizando o Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423058"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Adicione ou remova um grupo de outro grupo usando o Diretório Ativo Azure
Este artigo ajuda-o a adicionar e remover um grupo de outro grupo usando o Azure Ative Directory.

>[!Note]
>Se está a tentar eliminar o grupo de pais, consulte [como atualizar ou apagar um grupo e os seus membros](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Adicione um grupo a outro grupo
Você pode adicionar um grupo de Segurança existente a outro grupo de Segurança existente (também conhecido como grupos aninhados), criando um grupo membro (subgrupo) e um grupo-mãe. O grupo membro herda os atributos e propriedades do grupo dos pais, poupando-lhe tempo de configuração.

>[!Important]
>Atualmente não apoiamos:<ul><li>Adicionar grupos a um grupo sincronizado com o Diretório Ativo no local.</li><li>Adicionar grupos de segurança ao Office 365 grupos.</li><li>Adicionar o Office 365 grupos a grupos de segurança ou outros grupos do Office 365.</li><li>Atribuir aplicativos a grupos aninhados.</li><li>Aplicando licenças a grupos aninhados.</li><li>Adicionar grupos de distribuição em cenários de nidificação.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Para adicionar um grupo como membro de outro grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Grupos**.

3. Na página **grupos - Todos os grupos,** procurem e selecionem o grupo que se tornará membro de outro grupo. Para este exercício, estamos a usar a política do MDM- Grupo **Ocidental.**

    >[!Note]
    >Pode adicionar o seu grupo como membro a apenas um grupo de cada vez. Além disso, a caixa **Select Group** filtra o visor com base na correspondência da sua entrada com qualquer parte de um nome de utilizador ou dispositivo. No entanto, os personagens wildcard não são suportados.

    ![Grupos - Página de todos os grupos com política de MDM - Grupo Oeste selecionado](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na página de política do **MDM - West - Group memberships,** select Group **memberships**, select **Add**, localize o grupo que deseja que o seu grupo seja membro e, em seguida, escolha **Select**. Para este exercício, estamos a usar a política do MDM - Todos os grupos **org.**

    A **política do MDM - Grupo Ocidental** é agora membro da política do MDM - Todos os grupos **org,** herdando todas as propriedades e configuração da política do MDM - Todos os grupos org.

    ![Criar uma associação de grupo adicionando grupo a outro grupo](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Reveja a página de membros do MDM - West - Grupo para ver a relação entre o grupo e os **membros.**

6. Para uma visão mais detalhada da relação entre grupo e membro, selecione o nome do grupo (**política de MDM - All org**) e dê uma olhada na política do MDM - detalhes da página **oeste.**

## <a name="remove-a-group-from-another-group"></a>Remova um grupo de outro grupo
Pode remover um grupo de segurança existente de outro grupo de segurança. No entanto, a remoção do grupo também remove quaisquer atributos e propriedades herdados para os seus membros.

### <a name="to-remove-a-member-group-from-another-group"></a>Para remover um grupo membro de outro grupo
1. Na página **grupos - Todos os grupos,** procurem e selecionem o grupo que deve ser removido como membro de outro grupo. Para este exercício, estamos novamente a usar a política do MDM- Grupo **Ocidental.**

2. Sobre a política do MDM - página **geral ocidental,** selecione **membros do Grupo**.

3. Selecione a **política do MDM - Todos os grupos org** da política do MDM - página de **membros** do Grupo Ocidental e, em seguida, selecione **Remover** da política do MDM - Detalhes da página **oeste.**

    ![Página de membros do grupo mostrando tanto o membro como os detalhes do grupo](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Utilizar um grupo para gerir o acesso a aplicações SaaS](../users-groups-roles/groups-saasapps.md)

- [Cenários, limitações e questões conhecidas usando grupos para gerir licenciamento no Diretório Ativo Azure](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
