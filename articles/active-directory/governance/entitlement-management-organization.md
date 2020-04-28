---
title: Adicione uma organização conectada na gestão de direitos da Azure AD - Azure Ative Directory
description: Saiba como permitir que pessoas fora da sua organização solicitem pacotes de acesso para que possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128614"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicione uma organização conectada na gestão de direitos da Azure AD

Com a gestão de direitos da Azure Ative Directory (Azure AD), pode colaborar com pessoas fora da sua organização. Se colaborar frequentemente com os utilizadores num diretório ou domínio externo do Azure AD, pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que possa permitir que utilizadores fora da sua organização solicitem recursos no seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio externo da AD Azure com o que tem uma relação.

Por exemplo, suponha que trabalhe no Woodgrove Bank e queira colaborar com duas organizações externas. Estas duas organizações têm configurações diferentes:

- O Graphic Design Institute utiliza o Azure AD, e os seus utilizadores têm um nome principal de utilizador que termina com *graphicdesigninstitute.com*.
- Contoso ainda não usa Azure AD. Os utilizadores da Contoso têm um nome principal do utilizador que termina com *contoso.com*.

Neste caso, pode configurar duas organizações conectadas. Cria-se uma organização conectada para o Graphic Design Institute e outra para o Contoso. Se, em seguida, adicionar as duas organizações conectadas a uma política, os utilizadores de cada organização com um nome principal de utilizador que corresponda à política podem solicitar pacotes de acesso. Os utilizadores com um nome principal do utilizador que tenha um domínio de *graphicdesigninstitute.com* corresponderiam à organização ligada ao Graphic Design Institute e seriam autorizados a submeter pedidos. Os utilizadores com um nome principal de utilizador que tenha um domínio de *contoso.com* corresponderiam à organização ligada ao Contoso e também seriam autorizados a solicitar pacotes. E, como o Graphic Design Institute utiliza o Azure AD, qualquer utilizador com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) que seja adicionado ao seu inquilino, como *o graphicdesigninstitute.exemplo,* também poderia solicitar pacotes de acesso utilizando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os utilizadores do diretório ou autenticação de domínio azure dependem do tipo de autenticação. Os tipos de autenticação para organizações conectadas são:

- Azure AD
- [Federação direta](../b2b/direct-federation.md)
- [Senha única](../b2b/one-time-passcode.md) (domínio)

Para uma demonstração de como adicionar uma organização conectada, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização associada

Para adicionar um diretório ou domínio externo do Azure AD como uma organização conectada, siga as instruções nesta secção.

**Função pré-requisito**: *Administrador global,* *administrador de utilizador*ou *convidado* convidado

1. No portal Azure, selecione **Azure Ative Directory**, e, em seguida, selecione **Identity Governance**.

1. No painel esquerdo, selecione **Organizações Conectadas**, e, em seguida, selecione **Adicionar organização conectada**.

    ![O botão "Adicionar organização conectada"](./media/entitlement-management-organization/connected-organization.png)

1. Selecione o separador **Basics** e, em seguida, introduza um nome e descrição de exibição para a organização.

    ![O painel básico "Adicionar organização conectada"](./media/entitlement-management-organization/organization-basics.png)

1. Selecione o separador **de domínio Diretório +** e, em seguida, selecione Adicionar **diretório + domínio**.

    Abre-se o painel **seletos + domínios.**

1. Na caixa de pesquisa, introduza um nome de domínio para procurar o diretório ou domínio azure AD. Certifique-se de introduzir todo o nome de domínio.

1. Verifique se o nome da organização e o tipo de autenticação estão corretos. A forma como os utilizadores se inscrevem depende do tipo de autenticação.

    ![O painel "Select diretórios + domínios"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecione **Adicionar** para adicionar o diretório ou domínio Azure AD. Atualmente, você pode adicionar apenas um diretório De Azure ou domínio por organização conectada.

    > [!NOTE]
    > Todos os utilizadores do diretório ou domínio da AD Azure poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pelo negócio da AD Azure para o negócio (B2B) permitir ou negar lista. Para mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas](../b2b/allow-deny-list.md).

1. Depois de adicionar o diretório ou domínio Azure AD, selecione **Select**.

    A organização aparece na lista.

    ![O painel "Diretório + domínio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecione o separador **Patrocinadores** e, em seguida, adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são utilizadores internos ou externos já no seu diretório que são o ponto de contacto para a relação com esta organização conectada. Patrocinadores internos são utilizadores membros no seu diretório. Patrocinadores externos são utilizadores convidados da organização conectada que foram previamente convidados e já estão no seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os utilizadores desta organização conectada solicitarem o acesso a este pacote de acesso. Para obter informações sobre como convidar um utilizador convidado para o seu diretório, consulte os utilizadores de colaboração Add [Azure Ative Directory B2B no portal Azure](../b2b/add-users-administrator.md).

    Quando selecionar **Adicionar/Remover,** abre-se um painel no qual pode escolher patrocinadores internos ou externos. O painel apresenta uma lista não filtrada de utilizadores e grupos no seu diretório.

    ![O painel de Patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecione o **'Rever + crie** o separador', reveja as definições da sua organização e, em seguida, selecione **Criar**.

    ![O painel "Review + criar"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualizar uma organização conectada 

Se a organização conectada mudar para um domínio diferente, o nome da organização muda, ou se quiser alterar os patrocinadores, pode atualizar a organização conectada seguindo as instruções nesta secção.

**Função pré-requisito**: *Administrador global,* *administrador de utilizador*ou *convidado* convidado

1. No portal Azure, selecione **Azure Ative Directory**, e, em seguida, selecione **Identity Governance**.

1. No painel esquerdo, selecione **as organizações Connected**, e, em seguida, selecione a organização conectada para abri-la.

1. No painel de visão geral da organização conectada, **selecione Editar** para alterar o nome ou descrição da organização.  

1. No painel de **domínio Diretório +,** selecione **'Diretório de Atualização + domínio'** para mudar para um diretório ou domínio diferente.

1. No painel **de Patrocinadores,** selecione **Adicionar patrocinadores internos** ou **adicionar patrocinadores externos** para adicionar um utilizador como patrocinador. Para remover um patrocinador, selecione o patrocinador e, no painel certo, selecione **Delete**.


## <a name="delete-a-connected-organization"></a>Eliminar uma organização conectada

Se já não tiver uma relação com um diretório ou domínio externo da AD, pode eliminar a organização conectada.

**Função pré-requisito**: *Administrador global,* *administrador de utilizador*ou *convidado* convidado

1. No portal Azure, selecione **Azure Ative Directory**, e, em seguida, selecione **Identity Governance**.

1. No painel esquerdo, selecione **as organizações Connected**, e, em seguida, selecione a organização conectada para abri-la.

1. No painel de visão geral da organização conectada, **selecione Eliminar** para eliminá-lo.

    Atualmente, só é possível eliminar uma organização conectada se não existirem utilizadores conectados.

    ![A organização conectada Eliminar botão](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso dos utilizadores externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Regule o acesso para utilizadores que não estão no seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
