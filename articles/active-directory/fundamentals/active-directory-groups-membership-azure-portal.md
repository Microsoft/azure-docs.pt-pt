---
title: Adicionar ou remover um grupo de outro grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar ou remover um grupo de outro grupo com o Azure Active Directory.
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
ms.openlocfilehash: 896a8b2c96dc10afbc3f179aed5a8497a8948ca9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561938"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Adicionar ou remover um grupo de outro grupo com o Azure Active Directory
Este artigo ajuda-o a adicionar e remover um grupo de outro grupo com o Azure Active Directory.

>[!Note]
>Se estiver a tentar eliminar o grupo principal, consulte [como atualizar ou eliminar um grupo e seus membros](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Adicionar um grupo para outro grupo
Pode adicionar um grupo de segurança existente para outro grupo de segurança existente (também conhecido como aninhados grupos), criando um membro de grupo (subgrupo) e um grupo principal. O grupo de membro herda os atributos e propriedades do grupo principal, poupando tempo de configuração.

>[!Important]
>Não são atualmente suportados:<ul><li>Adicionar grupos a um grupo sincronizado com Active Directory locais.</li><li>Adicionando grupos de segurança a grupos do Office 365.</li><li>Adicionar grupos do Office 365 a grupos de segurança ou a outros grupos do Office 365.</li><li>Atribuindo aplicativos a grupos aninhados.</li><li>Aplicando licenças a grupos aninhados.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Para adicionar um grupo como membro de outro grupo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **grupos**.

3. Sobre o **grupos – todos os grupos** página, procure e selecione o grupo que está a ser membro de outro grupo. Para este exercício, estamos a utilizar o **política MDM - oeste** grupo.

    >[!Note]
    >Pode adicionar seu grupo como membro a apenas um grupo de cada vez. Além disso, o **selecionar grupo** caixa filtra a exibição com base na correspondência de sua participação a qualquer parte de um nome de utilizador ou dispositivo. No entanto, não são suportados carateres universais.

    ![Grupo de oeste grupos - página todos os grupos com a política MDM - selecionado](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na **associações de grupo - oeste - política MDM** página, selecione **associações de grupo**, selecione **adicionar**, localize o grupo ao qual pretende que o seu grupo para ser um membro do e, em seguida, selecione  **Selecione**. Para este exercício, estamos a utilizar o **política MDM - todos os org** grupo.

    O **política MDM - oeste** grupo agora é um membro da **política MDM - org todos os** grupo, herdando todas as propriedades e configuração da política de MDM - todos os grupo de organização.

    ![Criar uma associação de grupo ao adicionar o grupo para outro grupo](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Reveja os **associações de grupo do MDM política - oeste -** página para ver a relação do grupo e membro.

    ![Página associações MDM política - oeste - grupo mostrando o grupo principal](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Para uma visão mais detalhada da relação de grupo e membro, selecione o nome do grupo (**política MDM - org todos os**) e dar uma olhada a **política MDM - oeste** página de detalhes.

    ![Página de associação de grupo mostrando o membro e os detalhes do grupo](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-group-from-another-group"></a>Remover um grupo de outro grupo
Pode remover um grupo de segurança existente a outro grupo de segurança. No entanto, remover o grupo também remove quaisquer propriedades e atributos herdados para seus membros.

### <a name="to-remove-a-member-group-from-another-group"></a>Para remover um grupo de membro de outro grupo
1. Sobre o **grupos – todos os grupos** página, procure e selecione o grupo que está a ser removidos como um membro de outro grupo. Para este exercício, estamos novamente usando o **política MDM - oeste** grupo.

2. Sobre o **política MDM - descrição geral do Oeste** página, selecione **associações de grupo**.

    ![Política MDM - página de descrição geral do oeste](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Selecione o **política MDM - org todos os** grupo da **associações de grupo do MDM política - oeste -** página e, em seguida, selecione **remover** do **política da MDM - oeste** página de detalhes.

    ![Página de associação de grupo mostrando o membro e os detalhes do grupo](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Utilizar um grupo para gerir o acesso a aplicações SaaS](../users-groups-roles/groups-saasapps.md)

- [Cenários, limitações e problemas conhecidos, utilizar grupos para gerir o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
