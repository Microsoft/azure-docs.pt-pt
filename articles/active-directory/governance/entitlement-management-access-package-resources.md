---
title: Alterar funções de recursos para um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como alterar as funções de recurso para um pacote de acesso existente na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644185"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar funções de recursos para um pacote de acesso na gestão de direitos Azure AD

Como gestor de pacotes de acesso, pode alterar os recursos num pacote de acesso a qualquer momento sem se preocupar em providenciar o acesso do utilizador aos novos recursos ou remover o seu acesso dos recursos anteriores. Este artigo descreve como alterar as funções de recurso para um pacote de acesso existente.

Este vídeo fornece uma visão geral de como alterar um pacote de acesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Verifique o catálogo de recursos

Se precisa de adicionar recursos a um pacote de acesso, deve verificar se os recursos que necessita estão disponíveis no catálogo. Se você é um gestor de pacotes de acesso, você não pode adicionar recursos a um catálogo, mesmo que você possua eles. Está restrito a utilizar os recursos disponíveis no catálogo.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogo** e, em seguida, abra o catálogo.

1. No menu esquerdo, clique em **Recursos** para ver a lista de recursos neste catálogo.

    ![Lista de recursos em um catálogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se você é um gestor de pacotes de acesso e precisa adicionar recursos ao catálogo, você pode pedir ao proprietário do catálogo para adicioná-los.

## <a name="add-resource-roles"></a>Adicionar funções de recursos

Uma função de recurso é uma coleção de permissões associadas a um recurso. A forma como disponibiliza recursos para os utilizadores solicitarem é adicionando funções de recursos ao seu pacote de acesso. Pode adicionar funções de recursos para grupos, equipas, aplicações e sites SharePoint.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. No menu esquerdo, clique nas **funções de Recurso.**

1. Clique **em Adicionar funções de recurso** para abrir as funções de recurso Adicionar à página do pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Dependendo se pretende adicionar um site de grupo, equipa, aplicação ou SharePoint, execute os passos numa das seguintes secções de função de recursos.

## <a name="add-a-group-or-team-resource-role"></a>Adicione um papel de grupo ou de recursos de equipa

Pode ter a gestão de direitos adicionar automaticamente os utilizadores a um grupo ou a uma equipa nas Equipas da Microsoft quando lhes for atribuído um pacote de acesso. 

- Quando um grupo ou equipa faz parte de um pacote de acesso e um utilizador é designado para esse pacote de acesso, o utilizador é adicionado a esse grupo ou equipa, se ainda não estiver presente.
- Quando a atribuição do pacote de acesso de um utilizador expira, são removidas do grupo ou da equipa, a menos que tenham atualmente uma atribuição a outro pacote de acesso que inclua esse mesmo grupo ou equipa.

Pode selecionar qualquer [grupo de segurança AD AZure ou Microsoft 365 Group](../fundamentals/active-directory-groups-create-azure-portal.md). Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários de catálogos podem adicionar qualquer grupo ao catálogo se forem proprietários do grupo. Tenha em mente as seguintes restrições AD AZure ao selecionar um grupo:

- Quando um utilizador, incluindo um hóspede, é adicionado como membro a um grupo ou equipa, pode ver todos os outros membros desse grupo ou equipa.
- O Azure AD não pode alterar a adesão de um grupo que foi sincronizado a partir do Windows Server Ative Directory usando o Azure AD Connect, ou que foi criado no Exchange Online como um grupo de distribuição.  
- A adesão a grupos dinâmicos não pode ser atualizada adicionando ou removendo um membro, pelo que os membros dinâmicos do grupo não são adequados para utilização com a gestão de direitos.

Para obter mais informações, consulte [Grupos Compare](/office365/admin/create-groups/compare-groups) e [Grupos Microsoft 365 e Microsoft Teams](/microsoftteams/office-365-groups).

1. Nas **funções de recurso Adicionar para aceder à** página do pacote, clique em **Grupos e Equipas** para abrir o painel de grupos Select.

1. Selecione os grupos e equipas que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione grupos](./media/entitlement-management-access-package-resources/group-select.png)

1. Clique em **Selecionar**.

    Assim que selecionar o grupo ou a equipa, a coluna **sub-tipo** listará um dos seguintes subtipos:

    | Sub-tipo | Description |
    | --- | --- |
    | Segurança | Usado para conceder acesso a recursos. |
    | Distribuição | Usado para enviar notificações a um grupo de pessoas. |
    | Microsoft 365 | Microsoft 365 Group que não está ativado por Equipas. Utilizado para colaboração entre utilizadores, dentro e fora da sua empresa. |
    | Equipa | Microsoft 365 Group que está ativado por Equipas. Utilizado para colaboração entre utilizadores, dentro e fora da sua empresa. |

1. Na lista **de funções,** selecione **Proprietário** ou **Membro**.

    Normalmente seleciona o papel de Membro. Se selecionar a função Proprietário, isso permitirá aos utilizadores adicionar ou remover outros membros ou proprietários.

    ![Pacote de acesso - Adicione o papel de recurso para um grupo ou equipa](./media/entitlement-management-access-package-resources/group-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizadores com atribuições existentes ao pacote de acesso tornar-se-ão automaticamente membros deste grupo ou equipa quando este for adicionado.

## <a name="add-an-application-resource-role"></a>Adicione uma função de recurso de aplicação

Pode ter o Azure AD a atribuir automaticamente aos utilizadores o acesso a uma aplicação empresarial Azure AD, incluindo aplicações SaaS e aplicações da sua organização federadas à Azure AD, quando um utilizador recebe um pacote de acesso. Para aplicações que se integram com a Azure AD através de um único sign-on federado, a Azure AD emitirá fichas da federação para utilizadores designados para a aplicação.

As aplicações podem ter múltiplas funções. Ao adicionar uma aplicação a um pacote de acesso, se essa aplicação tiver mais do que uma função, terá de especificar a função adequada para esses utilizadores. Se estiver a desenvolver aplicações, pode ler mais sobre como essas funções são adicionadas às suas aplicações em [Como: Configurar a alegação de papel emitida no token SAML para aplicações empresariais](../develop/active-directory-enterprise-app-role-management.md).

Uma vez que uma função de aplicação faz parte de um pacote de acesso:

- Quando um utilizador é atribuído a esse pacote de acesso, o utilizador é adicionado a essa função de aplicação, se ainda não estiver presente.
- Quando a atribuição do pacote de acesso de um utilizador expirar, o seu acesso será removido da aplicação, a menos que tenha uma atribuição a outro pacote de acesso que inclua essa função de aplicação.

Aqui ficam algumas considerações ao selecionar uma aplicação:

- As candidaturas também podem ter grupos atribuídos às suas funções.  Pode optar por adicionar um grupo no lugar de uma função de aplicação num pacote de acesso, no entanto a aplicação não será visível para o utilizador como parte do pacote de acesso no portal My Access.

1. Nas **funções de recurso Adicionar para aceder à** página do pacote, clique em **Aplicações** para abrir o painel de aplicações Select.

1. Selecione as aplicações que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione aplicações](./media/entitlement-management-access-package-resources/application-select.png)

1. Clique em **Selecionar**.

1. Na lista **de Funções,** selecione uma função de candidatura.

    ![Pacote de acesso - Adicionar função de recurso para uma aplicação](./media/entitlement-management-access-package-resources/application-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizadores com atribuições existentes ao pacote de acesso terá automaticamente acesso a esta aplicação quando este for adicionado.

## <a name="add-a-sharepoint-site-resource-role"></a>Adicione uma função de recursos do site SharePoint

O Azure AD pode atribuir automaticamente aos utilizadores o acesso a um site Online SharePoint ou a uma coleção de sites Do SharePoint Online quando lhes for atribuído um pacote de acesso.

1. Nas **funções de recurso Adicionar para aceder à** página do pacote, clique nos sites **SharePoint** para abrir o painel de sites Select SharePoint Online.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="Pacote de acesso - Adicionar funções de recursos - Selecione sites SharePoint - Vista do Portal":::

1. Selecione os sites SharePoint Online que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione sites Online Do SharePoint](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na lista **Role,** selecione uma função de site SharePoint Online.

    ![Pacote de acesso - Adicione a função de recurso para um site SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizadores com atribuições existentes ao pacote de acesso terá automaticamente acesso a este site SharePoint Online quando este for adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recursos

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. No menu esquerdo, clique nas **funções de Recurso.**

1. Na lista de funções de recursos, encontre o papel de recurso que pretende remover.

1. Clique na elipse **(...)** e, em seguida, clique em **Remover a função de recurso**.

    Qualquer utilizadores com atribuições existentes ao pacote de acesso terão automaticamente o seu acesso revogado a esta função de recurso quando este for removido.

## <a name="when-changes-are-applied"></a>Quando as alterações são aplicadas

Na gestão de direitos, a Azure AD processará alterações a granel para atribuição e recursos nos seus pacotes de acesso várias vezes ao dia. Assim, se fizer uma atribuição ou alterar as funções de recurso do seu pacote de acesso, pode demorar até 24 horas para que essa alteração seja feita em Azure AD, mais o tempo que leva para propagar essas alterações a outros Serviços Microsoft Online ou aplicações SaaS conectadas. Se a sua alteração afetar apenas alguns objetos, a alteração provavelmente levará apenas alguns minutos a aplicar-se em Azure AD, após o que outros componentes AD Azure detetarão então essa alteração e atualização das aplicações SaaS. Se a sua mudança afetar milhares de objetos, a mudança demorará mais tempo. Por exemplo, se tiver um pacote de acesso com 2 aplicações e 100 atribuições de utilizadores, e decidir adicionar uma função de site SharePoint ao pacote de acesso, pode haver um atraso até que todos os utilizadores faziam parte dessa função do site SharePoint. Pode monitorizar o progresso através do registo de auditoria Azure AD, do registo de provisões Azure AD e dos registos de auditoria do site SharePoint.

Quando remove um membro de uma equipa, eles também são removidos do Grupo Microsoft 365. A remoção da funcionalidade de chat da equipa poderá ser atrasada. Para mais informações, consulte [a adesão ao Grupo.](/microsoftteams/office-365-groups#group-membership)

## <a name="next-steps"></a>Passos seguintes

- [Crie um grupo básico e adicione membros usando o Azure Ative Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Como: Configurar a alegação de papel emitida no token SAML para aplicações empresariais](../develop/active-directory-enterprise-app-role-management.md)
- [Introdução ao SharePoint Online](/sharepoint/introduction)