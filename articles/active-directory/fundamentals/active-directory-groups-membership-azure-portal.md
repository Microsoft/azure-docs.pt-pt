---
title: Adicionar ou remover um grupo de outro grupo - Azure AD
description: Instruções sobre como adicionar ou remover um grupo de outro grupo utilizando o Azure Ative Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2abddefebdf58b6c4f82bd6e6e882e3cb5d16023
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604306"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Adicione ou remova um grupo de outro grupo usando o Azure Ative Directory
Este artigo ajuda-o a adicionar e remover um grupo de outro grupo usando o Azure Ative Directory.

>[!Note]
>Se estiver a tentar eliminar o grupo de pais, consulte [como atualizar ou eliminar um grupo e os seus membros](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Adicione um grupo a outro grupo
Pode adicionar um grupo de Segurança existente a outro grupo de Segurança existente (também conhecido como grupos aninhados), criando um grupo de membros (subgrupo) e um grupo de pais. O grupo membro herda os atributos e propriedades do grupo-mãe, poupando-lhe tempo de configuração.

>[!Important]
>Não apoiamos atualmente:<ul><li>Adicionar grupos a um grupo sincronizado com o Ative Directory no local.</li><li>Adicionar grupos de segurança ao Office 365 grupos.</li><li>Adicionar grupos do Office 365 a grupos de segurança ou outros grupos do Office 365.</li><li>Atribuindo aplicativos a grupos aninhados.</li><li>Aplicação de licenças a grupos aninhados.</li><li>Adicionar grupos de distribuição em cenários de nidificação.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Para adicionar um grupo como membro de outro grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**e, em seguida, selecione **Grupos**.

3. Na página **Grupos - Todos os grupos,** procure e selecione o grupo que se tornará membro de outro grupo. Para este exercício, estamos a usar a política do MDM- Grupo **Oeste.**

    >[!Note]
    >Pode adicionar o seu grupo como membro a apenas um grupo de cada vez. Além disso, a caixa **Select Group** filtra o visor com base na correspondência da sua entrada em qualquer parte de um utilizador ou nome do dispositivo. No entanto, os personagens wildcard não são suportados.

    ![Grupos - Página de todos os grupos com política de MDM - Grupo ocidental selecionado](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na página de política do **MDM - West - Grupo,** selecione **membros do Grupo**, selecione **Add**, localize o grupo em que pretende que o seu grupo seja membro e, em seguida, escolha **Select**. Para este exercício, estamos a usar a política do **MDM - Todos os grupos org.**

    A **política do MDM - Grupo Ocidental** é agora membro da política do MDM - All **org** group, herdando todas as propriedades e configuração da política do MDM - Todos os grupos org.

    ![Criar uma associação de grupo adicionando grupo a outro grupo](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Reveja a página de membros do grupo MDM - West - Group para ver a relação do grupo e dos **membros.**

6. Para uma visão mais detalhada da relação de grupo e membro, selecione o nome de grupo **(política MDM - All org**) e dê uma olhada na política do MDM - Detalhes da página **oeste.**

## <a name="remove-a-group-from-another-group"></a>Remova um grupo de outro grupo
Pode remover um grupo de segurança existente de outro grupo de Segurança. No entanto, a remoção do grupo também remove quaisquer atributos e propriedades herdados para os seus membros.

### <a name="to-remove-a-member-group-from-another-group"></a>Para remover um grupo de membros de outro grupo
1. Na página **Grupos - Todos os grupos,** procure e selecione o grupo que deve ser removido como membro de outro grupo. Para este exercício, estamos novamente a usar a política do MDM- Grupo **Ocidental.**

2. Na política do **MDM - Página geral do Oeste,** selecione **membros do Grupo**.

3. Selecione a **política do MDM - Todos os** grupos org da página de **membros** do GRUPO MDM - West - Group e, em seguida, **selecione Remover** da política do MDM - Detalhes da página **oeste.**

    ![Página de membros do grupo mostrando tanto os detalhes do membro como do grupo](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Utilizar um grupo para gerir o acesso a aplicações SaaS](../users-groups-roles/groups-saasapps.md)

- [Cenários, limitações e questões conhecidas usando grupos para gerir o licenciamento no Azure Ative Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
