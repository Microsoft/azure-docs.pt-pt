---
title: Alterar funções de recurso para um pacote de acesso na gestão de direitos da Azure AD - Diretório Ativo Azure
description: Saiba como alterar as funções de recurso para um pacote de acesso existente na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261895"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar funções de recurso para um pacote de acesso na gestão de direitos da AD Azure

Como gestor de pacotes de acesso, pode alterar os recursos num pacote de acesso a qualquer momento sem se preocupar em fornecer o acesso do utilizador aos novos recursos ou remover o seu acesso aos recursos anteriores. Este artigo descreve como alterar as funções de recurso para um pacote de acesso existente.

Este vídeo fornece uma visão geral de como alterar um pacote de acesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Verifique o catálogo de recursos

Se precisar de adicionar recursos a um pacote de acesso, deverá verificar se os recursos que necessita estão disponíveis no catálogo. Se é gestor de pacotes de acesso, não pode adicionar recursos a um catálogo, mesmo que os possua. Está restrito a utilizar os recursos disponíveis no catálogo.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogo** e abra o catálogo.

1. No menu à esquerda, clique em **Recursos** para ver a lista de recursos neste catálogo.

    ![Lista de recursos num catálogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se você é um gestor de pacotes de acesso e precisa adicionar recursos ao catálogo, você pode pedir ao proprietário do catálogo para adicioná-los.

## <a name="add-resource-roles"></a>Adicionar funções de recursos

Um papel de recursos é uma coleção de permissões associadas a um recurso. A forma como disponibiliza recursos para os utilizadores solicitarem é adicionando funções de recursos ao seu pacote de acesso. Pode adicionar funções de recursos para grupos, equipas, aplicações e sites SharePoint.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu esquerdo, clique nas **funções de Recurso.**

1. Clique em **adicionar funções de recurso** para abrir as funções de recurso Adicionar para aceder à página do pacote.

    ![Pacote de acesso - Adicionar funções de recursos](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Dependendo se pretende adicionar um site de grupo, equipa, aplicação ou SharePoint, execute os passos numa das seguintes secções de recurso.

## <a name="add-a-group-or-team-resource-role"></a>Adicione uma função de grupo ou de recursos de equipa

Pode ter uma gestão de direitos adicionando automaticamente os utilizadores a um grupo ou a uma equipa em Microsoft Teams quando lhes for atribuído um pacote de acesso. 

- Quando um grupo ou equipa faz parte de um pacote de acesso e um utilizador é atribuído a esse pacote de acesso, o utilizador é adicionado a esse grupo ou equipa, se não estiver presente.
- Quando a atribuição do pacote de acesso de um utilizador expira, eles são removidos do grupo ou equipa, a menos que tenham atualmente uma atribuição para outro pacote de acesso que inclua esse mesmo grupo ou equipa.

Pode selecionar qualquer grupo de [segurança Azure AD ou Office 365 Group](../fundamentals/active-directory-groups-create-azure-portal.md). Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários de catálogos podem adicionar qualquer grupo ao catálogo se forem proprietários do grupo. Tenha em mente os seguintes constrangimentos da AD Azure ao selecionar um grupo:

- Quando um utilizador, incluindo um hóspede, é adicionado como membro a um grupo ou equipa, pode ver todos os outros membros desse grupo ou equipa.
- A Azure AD não pode alterar a adesão de um grupo que foi sincronizado a partir do Windows Server Ative Directory utilizando o Azure AD Connect, ou que foi criado no Exchange Online como um grupo de distribuição.  
- A adesão a grupos dinâmicos não pode ser atualizada adicionando ou removendo um membro, pelo que as associações dinâmicas do grupo não são adequadas para utilização com a gestão de direitos.

Para mais informações, consulte [Compare grupos](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) e [Office 365 Groups e Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. Nas **funções de recurso Adicionar para aceder à** página do pacote, clique em **Grupos e Equipas** para abrir o painel de grupos Select.

1. Selecione os grupos e equipas que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione grupos](./media/entitlement-management-access-package-resources/group-select.png)

1. Clique em **Selecionar**.

    Uma vez selecionada o grupo ou equipa, a coluna **sub-tipo** listará um dos seguintes subtipos:

    |  |  |
    | --- | --- |
    | Segurança | Usado para conceder acesso aos recursos. |
    | Distribuição | Usado para enviar notificações a um grupo de pessoas. |
    | O365 | Grupo 365 do Office que não está habilitado para as equipas. Usado para colaboração entre utilizadores, dentro e fora da sua empresa. |
    | Equipa | Grupo 365 do Office que está habilitado para as equipas. Usado para colaboração entre utilizadores, dentro e fora da sua empresa. |

1. Na lista **de Funções,** selecione **Proprietário** ou **Membro**.

    Normalmente, seleciona a função de Membro. Se selecionar a função Proprietário, isso permitirá que os utilizadores adicionem ou removam outros membros ou proprietários.

    ![Pacote de acesso - Adicionar função de recurso para um grupo ou equipa](./media/entitlement-management-access-package-resources/group-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizador com atribuições existentes ao pacote de acesso tornar-se-á automaticamente membros deste grupo ou equipa quando este for adicionado.

## <a name="add-an-application-resource-role"></a>Adicione uma função de recurso de aplicação

Pode ter o Azure AD a atribuir automaticamente aos utilizadores acesso a uma aplicação empresarial Azure AD, incluindo aplicações SaaS e aplicações da sua organização federadas para a AD Azure, quando um utilizador é atribuído a um pacote de acesso. Para aplicações que se integrem com a Azure AD através de um único sign-on federado, a Azure AD emitirá fichas da federação para utilizadores atribuídos à aplicação.

As aplicações podem ter múltiplas funções. Ao adicionar uma aplicação a um pacote de acesso, se essa aplicação tiver mais de uma função, terá de especificar a função adequada para esses utilizadores. Se estiver a desenvolver aplicações, pode ler mais sobre como essas funções são adicionadas às suas aplicações em [Como: Configurar a alegação de função emitida no símbolo SAML para aplicações empresariais](../develop/active-directory-enterprise-app-role-management.md).

Uma vez que uma função de candidatura faça parte de um pacote de acesso:

- Quando é atribuído a um utilizador esse pacote de acesso, o utilizador é adicionado a essa função de aplicação, se não estiver já presente.
- Quando a atribuição do pacote de acesso de um utilizador expirar, o seu acesso será removido da aplicação, a menos que tenham uma atribuição para outro pacote de acesso que inclua essa função de aplicação.

Aqui ficam algumas considerações ao selecionar uma aplicação:

- As candidaturas também podem ter grupos atribuídos às suas funções.  Pode optar por adicionar um grupo no lugar de uma função de aplicação num pacote de acesso, no entanto, a aplicação não será visível para o utilizador como parte do pacote de acesso no portal My Access.

1. Na página adicionar recursos para aceder à página do **pacote,** clique em **Aplicações** para abrir o painel de aplicações Select.

1. Selecione as aplicações que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione aplicações](./media/entitlement-management-access-package-resources/application-select.png)

1. Clique em **Selecionar**.

1. Na lista **de Papéis,** selecione uma função de candidatura.

    ![Pacote de acesso - Adicionar função de recurso para uma aplicação](./media/entitlement-management-access-package-resources/application-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizador com atribuições existentes ao pacote de acesso terá automaticamente acesso a esta aplicação quando for adicionado.

## <a name="add-a-sharepoint-site-resource-role"></a>Adicione uma função de recurso do site SharePoint

A Azure AD pode atribuir automaticamente aos utilizadores acesso a um site SharePoint Online ou à recolha do site SharePoint Online quando lhes for atribuído um pacote de acesso.

1. Nas **funções de recurso Adicionar para aceder à** página do pacote, clique em sites **SharePoint** para abrir o painel de sites Select SharePoint Online.

1. Selecione os sites SharePoint Online que pretende incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione sites Online SharePoint](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na lista **de Papéis,** selecione uma função de site SharePoint Online.

    ![Pacote de acesso - Adicionar função de recurso para um site Online SharePoint](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Qualquer utilizador com atribuições existentes para o pacote de acesso terá automaticamente acesso a este site Do SharePoint Online quando for adicionado.

## <a name="remove-resource-roles"></a>Remover as funções de recurso

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu esquerdo, clique nas **funções de Recurso.**

1. Na lista de funções de recursos, encontre o papel de recurso que pretende remover.

1. Clique na elipse **(...)** e clique em remover a **função de recurso**.

    Qualquer utilizador com atribuições existentes ao pacote de acesso terá automaticamente o seu acesso revogado desta função de recurso quando este for removido.

## <a name="when-changes-are-applied"></a>Quando são aplicadas alterações

Na gestão de direitos, a Azure AD irá processar alterações a granel para atribuição e recursos nos seus pacotes de acesso várias vezes ao dia. Assim, se fizer uma atribuição, ou alterar as funções de recursos do seu pacote de acesso, pode demorar até 24 horas para que essa alteração seja feita em Azure AD, além do tempo que leva para propagar essas alterações a outros Serviços Online da Microsoft ou SaaS conectados aplicações. Se a sua alteração afetar apenas alguns objetos, a alteração provavelmente levará apenas alguns minutos para ser aplicada em Azure AD, após o que outros componentes da AD Azure detetarão então essa alteração e atualização das aplicações SaaS. Se a sua mudança afetar milhares de objetos, a mudança demorará mais tempo. Por exemplo, se tiver um pacote de acesso com 2 aplicações e 100 atribuições de utilizador, e decidir adicionar uma função de site sharePoint ao pacote de acesso, pode haver um atraso até que todos os utilizadores façam parte dessa função do site SharePoint. Pode monitorizar o progresso através do registo de auditoria da AD Azure, do registo de provisionamento de AD Azure e dos registos de auditoria do site SharePoint.

Quando remove um membro de uma equipa, este também é removido do Grupo do Office 365. A remoção da funcionalidade de chat da equipa poderá ser atrasada. Para mais informações, consulte [a adesão ao Grupo.](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)

## <a name="next-steps"></a>Passos seguintes

- [Crie um grupo básico e adicione membros usando o Diretório Ativo Azure](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Como: Configurar a alegação de função emitida no símbolo SAML para aplicações empresariais](../develop/active-directory-enterprise-app-role-management.md)
- [Introdução ao SharePoint Online](/sharepoint/introduction)