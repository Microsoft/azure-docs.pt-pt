---
title: Editar e gerenciar um pacote do Access existente no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como editar e gerenciar um pacote do Access existente no gerenciamento de direitos Azure Active Directory (versão prévia).
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
ms.date: 09/26/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a79cf166025ced6cb08d2f9e24801ea498fdc1c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326380"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Editar e gerenciar um pacote do Access existente no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um pacote do Access permite que você faça uma configuração única de recursos e políticas que administram automaticamente o acesso para a vida útil do pacote de acesso. Como um Gerenciador de pacotes do Access, você pode alterar os recursos em um pacote do Access a qualquer momento sem se preocupar com o provisionamento do acesso do usuário aos novos recursos ou a remoção de seu acesso dos recursos anteriores. As políticas também podem ser atualizadas a qualquer momento. no entanto, as alterações de política só afetam novos acessos.

Este artigo descreve como editar e gerenciar pacotes de acesso existentes.

## <a name="add-resource-roles"></a>Adicionar funções de recurso

Uma função de recurso é uma coleção de permissões associadas a um recurso. A maneira de disponibilizar os recursos para os usuários solicitarem é adicionando funções de recurso ao seu pacote de acesso. Você pode adicionar funções de recursos para grupos, equipes, aplicativos e sites do SharePoint.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Clique em **adicionar funções de recurso** para abrir a página Adicionar funções de recurso ao pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Dependendo se você deseja adicionar um grupo, uma equipe, um aplicativo ou um site do SharePoint, execute as etapas em uma das seções de função de recurso a seguir.

### <a name="add-a-group-or-team-resource-role"></a>Adicionar uma função de recurso de equipe ou grupo

Você pode ter o gerenciamento de direitos para adicionar automaticamente usuários a um grupo ou a uma equipe da Microsoft quando eles forem atribuídos a um pacote de acesso. 

- Quando um grupo ou equipe faz parte de um pacote do Access e um usuário é atribuído a esse pacote do Access, o usuário é adicionado a esse grupo ou equipe, se ainda não estiver presente.
- Quando a atribuição de pacote de acesso de um usuário expira, elas são removidas do grupo ou da equipe, a menos que tenham atualmente uma atribuição a outro pacote de acesso que inclua o mesmo grupo ou equipe.

Você pode selecionar qualquer [grupo de segurança do Azure ad ou grupo do Office 365](../fundamentals/active-directory-groups-create-azure-portal.md).  Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários do catálogo podem adicionar qualquer grupo ao catálogo se eles forem proprietários do grupo. Tenha em mente as seguintes restrições do Azure AD ao selecionar um grupo:

- Quando um usuário, incluindo um convidado, é adicionado como um membro a um grupo ou equipe, ele pode ver todos os outros membros desse grupo ou equipe.
- O Azure AD não pode alterar a associação de um grupo que foi sincronizado do Windows Server Active Directory usando Azure AD Connect ou que foi criado no Exchange Online como um grupo de distribuição.  
- A associação de grupos dinâmicos não pode ser atualizada com a adição ou remoção de um membro, portanto, as associações de grupo dinâmicos não são adequadas para uso com o gerenciamento de direitos.

Para obter mais informações, consulte [comparar grupos](/office365/admin/create-groups/compare-groups) e [grupos do Office 365 e Microsoft Teams](/microsoftteams/office-365-groups).

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **grupos e equipes** para abrir o painel Selecionar grupos.

1. Selecione os grupos e as equipes que você deseja incluir no pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso-selecionar grupos](./media/entitlement-management-access-package-edit/group-select.png)

1. Clique em **Selecionar**.

    Depois de selecionar o grupo ou a equipe, a coluna de **subtipo** listará um dos seguintes subtipos:

    |  |  |
    | --- | --- |
    | Segurança | Usado para conceder acesso aos recursos. |
    | Distribuição | Usado para enviar notificações para um grupo de pessoas. |
    | O365 | Grupo do Office 365 que não é habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |
    | Equipa | Grupo do Office 365 habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |

1. Na lista **função** , selecione **proprietário** ou **membro**.

    Normalmente, você seleciona a função de membro. Se você selecionar a função proprietário, isso permitirá que os usuários adicionem ou removam outros membros ou proprietários.

    ![Pacote de acesso-Adicionar função de recurso para um grupo ou equipe](./media/entitlement-management-access-package-edit/group-role.png)

1. Clique em **Adicionar**.

    Qualquer usuário com atribuições existentes ao pacote de acesso se tornará automaticamente membros deste grupo ou equipe quando ele for adicionado.

### <a name="add-an-application-resource-role"></a>Adicionar uma função de recurso de aplicativo

Você pode fazer com que o Azure AD atribua automaticamente aos usuários o acesso a um aplicativo empresarial do Azure AD, incluindo aplicativos SaaS e aplicativos de sua organização federados ao Azure AD, quando um usuário recebe um pacote de acesso. Para aplicativos que se integram ao Azure AD por meio de logon único federado, o Azure AD emitirá tokens de Federação para usuários atribuídos ao aplicativo.

Os aplicativos podem ter várias funções. Ao adicionar um aplicativo a um pacote do Access, se esse aplicativo tiver mais de uma função, será necessário especificar a função apropriada para esses usuários.  Se você estiver desenvolvendo aplicativos, poderá ler mais sobre como essas funções são fornecidas aos seus aplicativos no artigo sobre como [Configurar a declaração de função emitida no token SAML](../develop/active-directory-enterprise-app-role-management.md).

Quando uma função de aplicativo faz parte de um pacote de acesso:

- Quando um usuário recebe esse pacote de acesso, o usuário é adicionado a essa função de aplicativo, se ainda não estiver presente.
- Quando a atribuição de pacote de acesso de um usuário expirar, seu acesso será removido do aplicativo, a menos que eles tenham uma atribuição a outro pacote de acesso que inclua essa função de aplicativo.

Aqui estão algumas considerações ao selecionar um aplicativo:

- Os aplicativos também podem ter grupos atribuídos às suas funções.  Você pode optar por adicionar um grupo no lugar de uma função de aplicativo em um pacote do Access, no entanto, o aplicativo não ficará visível para o usuário como parte do pacote de acesso no meu portal de acesso.

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **aplicativos** para abrir o painel Selecionar aplicativos.

1. Selecione os aplicativos que você deseja incluir no pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso-selecionar aplicativos](./media/entitlement-management-access-package-edit/application-select.png)

1. Clique em **Selecionar**.

1. Na lista **função** , selecione uma função de aplicativo.

    ![Pacote de acesso-Adicionar função de recurso para um aplicativo](./media/entitlement-management-access-package-edit/application-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes ao pacote de acesso receberão automaticamente acesso a esse aplicativo quando ele for adicionado.

### <a name="add-a-sharepoint-site-resource-role"></a>Adicionar uma função de recurso de site do SharePoint

O Azure AD pode atribuir automaticamente acesso de usuários a um site do SharePoint Online ou a um conjunto de sites do SharePoint Online quando eles recebem um pacote de acesso.

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **sites do SharePoint** para abrir o painel selecionar sites do SharePoint Online.

1. Selecione os sites do SharePoint Online que você deseja incluir no pacote de acesso.

    ![Pacote de acesso-adicionar funções de recurso-selecionar sites do SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na lista **função** , selecione uma função de site do SharePoint Online.

    ![Pacote de acesso-Adicionar função de recurso para um site do SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes ao pacote de acesso receberão automaticamente acesso a esse site do SharePoint Online quando ele for adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recurso

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Na lista de funções de recurso, localize a função de recurso que você deseja remover.

1. Clique nas reticências ( **...** ) e, em seguida, clique em **remover função de recurso**.

    Todos os usuários com atribuições existentes ao pacote de acesso terão automaticamente seu acesso revogado para essa função de recurso quando ele for removido.

## <a name="add-a-new-policy"></a>Adicionar uma nova política

A maneira de especificar quem pode solicitar um pacote de acesso é criar uma política. Você pode criar várias políticas para um único pacote de acesso se quiser permitir que diferentes conjuntos de usuários recebam atribuições com diferentes configurações de aprovação e expiração. Uma única política não pode ser usada para atribuir usuários internos e externos ao mesmo pacote de acesso. No entanto, você pode criar duas políticas no mesmo pacote de acesso, uma para usuários internos e outra para usuários externos. Se houver várias políticas que se aplicam a um usuário, elas serão solicitadas no momento da solicitação para selecionar a política à qual desejam ser atribuídos.

O diagrama a seguir mostra o processo de alto nível para criar uma política para um pacote do Access existente.

![Criar um processo de política](./media/entitlement-management-access-package-edit/policy-process.png)

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e em **Adicionar política**.

1. Digite um nome e uma descrição para a política.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Com base na sua seleção para **usuários que podem solicitar acesso**, execute as etapas em uma das seções de política a seguir.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Editar uma política existente

Você pode editar uma política a qualquer momento. Se você alterar a data de validade de uma política, a data de expiração das solicitações que já estão em um estado de aprovação pendente ou aprovado não será alterada.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que você deseja editar.

    O painel **detalhes da política** é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-access-package-edit/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Quando terminar, clique em **Atualizar**.

## <a name="directly-assign-a-user"></a>Atribuir um usuário diretamente

Em alguns casos, talvez você queira atribuir diretamente usuários específicos a um pacote do Access para que os usuários não precisem passar pelo processo de solicitar o pacote de acesso. Para atribuir usuários diretamente, o pacote de acesso deve ter uma política que permita atribuições diretas de administrador.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.

1. Clique em **nova atribuição** para abrir Adicionar usuário ao pacote do Access.

    ![Atribuições-Adicionar usuário ao pacote de acesso](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Clique em **Adicionar usuários** para selecionar os usuários aos quais você deseja atribuir o pacote do Access.

1. Na lista **selecionar política** , selecione uma política que tenha a configuração [nenhum (somente atribuições diretas do administrador)](#policy-none-administrator-direct-assignments-only) .

    Se esse pacote de acesso não tiver esse tipo de política, você poderá clicar em **criar nova política** para adicionar um.

1. Defina a data e a hora em que você deseja que a atribuição de usuários selecionados inicie e termine. Se uma data de término não for fornecida, as configurações de expiração da política serão usadas.

1. Opcionalmente, forneça uma justificativa para sua atribuição direta para manutenção de registros.

1. Clique em **Adicionar** para atribuir diretamente os usuários selecionados ao pacote de acesso.

    Depois de alguns instantes, clique em **Atualizar** para ver os usuários na lista atribuições.

## <a name="view-who-has-an-assignment"></a>Exibir quem tem uma atribuição

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **atribuições** para ver uma lista de atribuições ativas.

1. Clique em uma atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tenham todas as funções de recurso provisionadas corretamente, clique no status do filtro e selecione **entrega**.

    Você pode ver detalhes adicionais sobre erros de entrega, localizando a solicitação correspondente do usuário na página **solicitações** .

1. Para ver as atribuições expiradas, clique no status do filtro e selecione **expirado**.

1. Para baixar um arquivo CSV da lista filtrada, clique em **baixar**.

## <a name="view-requests"></a>Exibir solicitações

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique em uma solicitação específica para ver detalhes adicionais.

## <a name="view-a-requests-delivery-errors"></a>Exibir os erros de entrega de uma solicitação

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Selecione a solicitação que você deseja exibir.

    Se a solicitação tiver erros de entrega, o status da solicitação não **será entregue e o** substatus será **parcialmente entregue**.

    Se houver erros de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique na contagem para ver todos os erros de entrega da solicitação.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Você só pode cancelar uma solicitação pendente que ainda não foi entregue.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação que você deseja cancelar

1. No painel detalhes da solicitação, clique em **Cancelar solicitação**.

## <a name="copy-my-access-portal-link"></a>Link copiar meu portal do Access

A maioria dos usuários em seu diretório pode entrar no portal meu acesso e ver automaticamente uma lista de pacotes de acesso que eles podem solicitar. No entanto, para usuários externos de parceiros comerciais que ainda não estão em seu diretório, você precisará enviar um link que eles possam usar para solicitar um pacote de acesso. 

É importante que você copie todo o link meu portal de acesso ao enviá-lo para um parceiro comercial interno. Isso garante que o parceiro terá acesso ao portal do seu diretório para fazer sua solicitação. 

O link começará com "myaccess", incluirá uma dica de diretório e terminará com uma ID de pacote de acesso. Verifique se o link inclui todos os itens a seguir:

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

Desde que o pacote de acesso esteja habilitado para usuários externos e você tenha uma política para o diretório do usuário externo, o usuário externo poderá usar o link meu portal de acesso para solicitar o pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, copie o **link meu portal de acesso**.

    ![Visão geral do pacote de acesso – link meu portal de acesso](./media/entitlement-management-shared/my-access-portal-link.png)

1. Envie um email para o parceiro comercial externo. Eles podem compartilhar o link com seus usuários para solicitar o pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a configuração oculta

Os pacotes do Access são detectáveis por padrão. Isso significa que, se uma política permitir que um usuário solicite o pacote de acesso, ele verá automaticamente o pacote de acesso listado em seu portal meu acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, clique em **Editar**.

1. Defina a configuração **oculta** .

    Se definido como **não**, o pacote de acesso será listado no meu portal de acesso do usuário.

    Se definido como **Sim**, o pacote de acesso não será listado no meu portal de acesso do usuário. A única maneira de um usuário exibir o pacote de acesso é se eles tiverem o **link direto meu acesso do portal** para o pacote de acesso.

## <a name="delete"></a>Eliminar

Um pacote do Access só poderá ser excluído se não tiver nenhuma atribuição de usuário ativa.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remova o acesso para todos os usuários.

1. No menu à esquerda, clique em **visão geral** e, em seguida, clique em **excluir**.

1. Na mensagem de exclusão exibida, clique em **Sim**.

## <a name="when-are-changes-applied"></a>Quando as alterações são aplicadas

No gerenciamento de direitos, o Azure AD processará alterações em massa para atribuição e recursos em seus pacotes de acesso várias vezes por dia. Portanto, se você fizer uma atribuição ou alterar as funções de recurso do seu pacote de acesso, poderá levar até 24 horas para que essa alteração seja feita no Azure AD, além da quantidade de tempo que leva para propagar essas alterações para outros serviços online da Microsoft ou para o aplicativo SaaS conectado &. Se a alteração afetar apenas alguns objetos, a alteração provavelmente levará apenas alguns minutos para ser aplicada ao Azure AD, após o qual outros componentes do Azure AD detectarão essa alteração e atualizarão os aplicativos SaaS. Se a alteração afetar milhares de objetos, a alteração levará mais tempo. Por exemplo, se você tiver um pacote de acesso com 2 aplicativos e 100 atribuições de usuário e decidir adicionar uma função de site do SharePoint ao pacote de acesso, pode haver um atraso até que todos os usuários façam parte dessa função de site do SharePoint. Você pode monitorar o progresso por meio do log de auditoria do Azure AD, do log de provisionamento do Azure AD e dos logs de auditoria do site do SharePoint.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar um proprietário do catálogo ou um Gerenciador de pacotes do Access](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Solicitar notificações de processo e email](entitlement-management-process.md)
