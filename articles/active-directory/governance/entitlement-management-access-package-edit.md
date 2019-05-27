---
title: Editar e gerir um pacote de acesso existente na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como editar e gerir um pacote de acesso existente na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833172"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Editar e gerir um pacote de acesso existente na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um pacote de acesso permite-lhe fazer uma configuração única de recursos e as políticas que administra automaticamente o acesso para o ciclo de vida do pacote de acesso. Como um Gestor de acesso a pacote, pode alterar os recursos num pacote de acesso em qualquer altura sem se preocupar sobre o acesso do utilizador para os novos recursos de aprovisionamento ou remover o acesso dos recursos anteriores. As políticas também podem ser atualizadas em qualquer altura, no entanto, as alterações de diretiva afetam apenas acessos de novo.

Este artigo descreve como editar e gerir pacotes de acesso existentes.

## <a name="add-resource-roles"></a>Adicionar funções de recursos

Uma função de recursos é um conjunto de permissões associadas um recurso. É a forma como é disponibilizar recursos para que os utilizadores peçam adicionando funções de recursos ao seu pacote de acesso. Pode adicionar funções de recursos para grupos, aplicações e sites do SharePoint.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recursos**.

1. Clique em **adicionar funções de recursos** para abrir as adicionar funções de recursos para acessar a página de pacote.

    ![Aceder ao pacote - adicionar funções de recursos](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Dependendo se pretende adicionar um grupo, aplicação ou site do SharePoint, execute os passos de uma das seguintes secções de função de recursos.

### <a name="add-a-group-resource-role"></a>Adicionar uma função de grupo de recursos

Pode ter adicionar automaticamente utilizadores a um grupo quando são atribuídas um pacote de acesso de gestão de direitos. 

- Quando um grupo é parte de um pacote de acesso e um utilizador é atribuído a esse pacote de acesso, o utilizador é adicionado a esse grupo, se ainda não estiver presente.
- Quando expira a atribuição de pacote de acesso de um utilizador, mesmos serão removidos do grupo, a menos que eles têm atualmente uma atribuição a outro pacote de acesso que inclui esse mesmo grupo.

Pode selecionar qualquer grupo do Office 365 ou o grupo de segurança do Azure AD.  Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários de catálogo podem adicionar qualquer grupo ao catálogo de se eles estão o proprietário do grupo. Considere as seguintes restrições do Azure AD ao selecionar um grupo:

- Quando um utilizador, incluindo um convidado, é adicionado como um membro a um grupo, podem ver todos os outros membros desse grupo.
- O Azure AD não pode alterar a associação de um grupo que foi sincronizado do Windows Server Active Directory com o Azure AD Connect.  
- Não é possível atualizar a associação de grupos dinâmicos ao adicionar ou remover um membro, portanto, associações a grupos dinâmicos não são adequadas para utilização com a gestão de direitos.

1. Sobre o **adicionar funções de recursos para acessar o pacote** página, clique em **grupos** para abrir o painel de grupos selecionados.

1. Selecione os grupos que pretende incluir no pacote de acesso.

    ![Aceder ao pacote - adicionar funções de recursos – selecionar grupos](./media/entitlement-management-access-package-edit/group-select.png)

1. Clique em **Selecionar**.

1. Na **função** lista, selecione **proprietário** ou **membro**.

    Normalmente, selecione a função de membro. Se selecionar a função de proprietário, que permitirá aos utilizadores adicionar ou remover outros membros ou proprietários.

    ![Aceder ao pacote - Adicionar função de recursos para um grupo](./media/entitlement-management-access-package-edit/group-role.png)

1. Clique em **Adicionar**.

    Os utilizadores que tenham atribuições existentes para o pacote de acesso estarão automaticamente os membros deste grupo quando é adicionado.

### <a name="add-an-application-resource-role"></a>Adicionar uma função de recursos de aplicação

Pode ter o Azure AD atribuir automaticamente os utilizadores acesso a uma aplicação empresarial do Azure AD, incluindo aplicações SaaS e aplicações da sua organização federadas para o Azure AD, quando um utilizador é atribuído um pacote de acesso. Para aplicativos que se integram com o Azure AD através do início de sessão único federado, do Azure AD irá emitir tokens de Federação para os utilizadores atribuídos à aplicação.

As aplicações podem ter múltiplas funções. Ao adicionar uma aplicação para um pacote de acesso, se esse aplicativo tem mais de uma função, terá de especificar a função adequada para esses utilizadores.  Se estiver a desenvolver aplicativos, pode ler mais sobre como essas funções são fornecidos aos seus aplicativos no artigo sobre como [configurar a afirmação de função emitida no SAML token](../develop/active-directory-enterprise-app-role-management.md).

Depois de uma função de aplicação faz parte de um pacote de acesso:

- Quando um utilizador é atribuído esse pacote de acesso, o utilizador é adicionado a essa função de aplicação, se não estiver presente.
- Quando expira a atribuição de pacote de acesso de um utilizador, o acesso será removido da aplicação, a menos que tenham uma atribuição a outro pacote de acesso que inclui essa função de aplicação.

Aqui estão algumas considerações quando selecionar uma aplicação:

- Aplicativos também podem ter grupos atribuídos para as respetivas funções também.  Pode optar por adicionar um grupo em vez de uma função de aplicativo num pacote de acesso, no entanto, em seguida, o aplicativo não serão visível para o utilizador como parte do pacote de acesso no portal do meu acesso.

1. Sobre o **adicionar funções de recursos para acessar o pacote** página, clique em **aplicativos** para abrir o painel de selecionar aplicativos.

1. Selecione as aplicações que pretende incluir no pacote de acesso.

    ![Aceder ao pacote - adicionar funções de recursos - selecionar aplicativos](./media/entitlement-management-access-package-edit/application-select.png)

1. Clique em **Selecionar**.

1. Na **função** , selecione uma função de aplicação.

    ![Aceder ao pacote - Adicionar função de recursos para uma aplicação](./media/entitlement-management-access-package-edit/application-role.png)

1. Clique em **Adicionar**.

    Os utilizadores que tenham atribuições existentes para o pacote de acesso automaticamente terá acesso a esta aplicação quando é adicionado.

### <a name="add-a-sharepoint-site-resource-role"></a>Adicionar uma função de recursos de sites do SharePoint

O Azure AD pode atribuir automaticamente os utilizadores acesso a um site do SharePoint Online ou o conjunto de sites do SharePoint Online quando são atribuídas um pacote de acesso.

1. Sobre o **adicionar funções de recursos para acessar o pacote** página, clique em **sites do SharePoint** para abrir o painel de sites selecione SharePoint Online.

1. Selecione os sites do SharePoint Online que pretende incluir no pacote de acesso.

    ![Aceder ao pacote - adicionar funções de recursos - sites selecione SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na **função** , selecione uma função de site SharePoint Online.

    ![Aceder ao pacote - Adicionar função de recursos para um site do SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Os utilizadores que tenham atribuições existentes para o pacote de acesso automaticamente terá acesso a este site do SharePoint Online quando é adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recursos

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recursos**.

1. Na lista de funções de recursos, localize a função de recursos que pretende remover.

1. Clique nas reticências (**...** ) e, em seguida, clique em **remover função de recursos**.

    Os utilizadores que tenham atribuições existentes para o pacote de acesso terão automaticamente o respetivo acesso revogado a esta função de recursos, quando este é removido.

## <a name="add-a-new-policy"></a>Adicionar uma nova política

É da forma que especificar quem pode pedir um pacote de acesso criar uma política. Pode criar várias políticas para um pacote de acesso único para permitir diferentes conjuntos de utilizadores para ser concedida atribuições com aprovação diferente e definições de expiração. Uma única política não pode ser utilizada para atribuir usuários internos e externos para o mesmo pacote de acesso. No entanto, pode criar duas políticas no mesmo pacote de acesso – um para usuários internos e outra para usuários externos. Se houver várias diretivas que se aplicam a um utilizador, será solicitado a eles no momento do que o pedido para selecionar a política que gostariam de ser atribuídos a.

O diagrama seguinte mostra o processo de alto nível para criar uma política para um pacote de acesso existente.

![Criar um processo de política](./media/entitlement-management-access-package-edit/policy-process.png)

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **políticas** e, em seguida **Adicionar política**.

1. Escreva um nome e uma descrição para a política.

    ![Criar política com o nome e descrição](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Com base na sua seleção **usuários que podem pedir acesso**, execute os passos de uma das seguintes secções de política.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Editar uma política existente

Pode editar uma política a qualquer momento. Se alterar a data de expiração para uma política, a data de expiração para pedidos que já estão a ser uma aprovação pendente ou aprovado Estado não será alterado.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique em política que pretende editar.

    O **detalhes da política** painel abre-se na parte inferior da página.

    ![Pacote de acesso – painel de detalhes da política](./media/entitlement-management-access-package-edit/policy-details.png)

1. Clique em **editar** para editar a política.

    ![Pacote de acesso - política de edição](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Quando terminar, clique em **atualização**.

## <a name="directly-assign-a-user"></a>Atribuir diretamente um utilizador

Em alguns casos, pode querer atribuir diretamente o utilizadores específicos a um pacote de acesso para que os utilizadores não têm de passar pelo processo de solicitar que o pacote de acesso. Para atribuir diretamente a utilizadores, o pacote de acesso tem de ter uma política que permite ao administrador atribuições diretas.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.

1. Clique em **nova atribuição** para abrir Adicionar utilizador ao pacote de acesso.

    ![Atribuições de – adicionar utilizador ao pacote de acesso](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Clique em **adicionar utilizadores** para selecionar os utilizadores que pretende atribuir o pacote de acesso para.

1. Na **selecione a política** , selecione uma política com o [None (administrador direto atribuições apenas)](#policy-none-administrator-direct-assignments-only) definição.

    Se este pacote de acesso não tem este tipo de política, pode clicar **criar nova política** para adicionar um.

1. Defina a data e hora em que pretende que a atribuição dos utilizadores selecionados para começar e terminar. Se uma data de fim não for fornecida, definições de expiração da política serão utilizadas.

1. Opcionalmente, fornece uma justificação para a sua atribuição direta de registo a manter.

1. Clique em **adicionar** atribuir diretamente os utilizadores selecionados ao pacote de acesso.

    Após alguns instantes, clique em **atualizar** para ver os utilizadores na lista de atribuições.

## <a name="view-who-has-an-assignment"></a>Ver quem tem uma atribuição

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **atribuições** para ver uma lista de atribuições ativas.

1. Clique numa atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições de que não tinham a todas as funções de recursos provisionadas de maneira adequada, clique com o estado do filtro e selecione **Delivering**.

    Pode ver detalhes adicionais sobre os erros de entrega através da localização de solicitação correspondente do usuário sobre o **pedidos** página.

1. Para ver as atribuições de expirada, clique com o estado do filtro e selecione **expirado**.

1. Para transferir um ficheiro CSV de lista filtrada, clique em **transferir**.

## <a name="view-requests"></a>Ver pedidos

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **pedidos**.

1. Clique num pedido específico para ver detalhes adicionais.

## <a name="view-a-requests-delivery-errors"></a>Ver os erros de entrega de um pedido

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **pedidos**.

1. Selecione o pedido de que pretende visualizar.

    Se o pedido tem quaisquer erros de entrega, o estado do pedido será **Undelivered** e o substatus será **parcialmente entregue**.

    Se houver algum erro de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique a contagem para ver todos os erros de entrega da solicitação.

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Apenas pode cancelar um pedido pendente que ainda não foi entregue.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Clique em **pedidos**.

1. Clique em solicitar que pretende cancelar

1. No painel de detalhes de pedido, clique em **pedido de cancelamento**.

## <a name="copy-my-access-portal-link"></a>Copiar hiperligação do portal do meu acesso

A maioria dos utilizadores no seu diretório podem iniciar sessão portal do meu acesso e automaticamente ver uma lista de pacotes de acesso, que eles poderão solicitar. No entanto, para usuários de parceiros de negócios externos que ainda não estão a no seu diretório, terá enviar-lhes uma ligação que eles podem usar para pedir um pacote de acesso. Desde que o pacote de acesso está ativado para utilizadores externos e tiver uma política para o diretório do utilizador externo, o utilizador externo pode utilizar a hiperligação do portal do meu acesso para solicitar o pacote de acesso.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Na página Descrição geral, copie os **hiperligação do portal do meu acesso**.

    ![Descrição geral do pacote acesso - hiperligação do portal do meu acesso](./media/entitlement-management-shared/my-access-portal-link.png)

1. Mensagem de e-mail ou enviar a ligação para o seu parceiro de negócios externos. Podem partilhar a ligação com os seus utilizadores para o pacote de acesso do pedido.

## <a name="change-the-hidden-setting"></a>Alterar a definição de Hidden

Pacotes de acesso podem ser detetados por predefinição. Isso significa que se uma política permite que um usuário solicitar o pacote de acesso, automaticamente verá o pacote de acesso listado no seu portal de acesso My.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. Na página Descrição geral, clique em **editar**.

1. Definir o **Hidden** definição.

    Se definido como **não**, o pacote de acesso será listado no portal de meu acesso do utilizador.

    Se definido como **Sim**, o pacote de acesso não será apresentado no portal de meu acesso do utilizador. A única forma de um utilizador pode ver o pacote de acesso é se eles têm o direct **hiperligação do portal do meu acesso** para o pacote de acesso.

## <a name="delete"></a>Eliminar

Só pode ser eliminado um pacote de acesso, se tiver não existem atribuições de utilizador do Active Directory.

**Função de pré-requisitos:** Administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remover o acesso para todos os utilizadores.

1. No menu à esquerda, clique em **descrição geral** e, em seguida, clique em **eliminar**.

1. Na caixa de mensagem de eliminação que aparece, clique em **Sim**.

## <a name="when-are-changes-applied"></a>Quando são aplicadas as alterações

Na gestão de direitos do Azure AD irá processar alterações em massa para a atribuição e recursos nos seus pacotes de acesso várias vezes ao dia. Para que, se tornar uma atribuição, ou alterar funções de recursos do seu pacote de acesso, pode demorar até 24 horas para a alteração feita no Azure AD, mais a quantidade de tempo ele leva para propagar essas alterações para outros serviços Online da Microsoft ou ligados a aplicação SaaS s. Se sua alteração afeta apenas alguns objetos, a alteração provavelmente demorar apenas alguns minutos a aplicar-se no Azure AD, após o qual outros componentes do Azure AD, em seguida, irão detetar que alterar e atualizar as aplicações de SaaS. Se sua alteração afetar milhares de objetos, a alteração demora mais tempo. Por exemplo, se tiver um pacote de acesso com 2 aplicações e 100 atribuições de utilizador e optar por adicionar uma função de site do SharePoint para o pacote de acesso, pode haver um atraso até que todos os usuários sejam parte dessa função de site do SharePoint. Pode monitorizar o progresso por meio de log de auditoria do Azure AD, o registo de aprovisionamento do Azure AD e os registos de auditoria de site do SharePoint.

## <a name="next-steps"></a>Passos Seguintes

- [Processo e notificações de e-mail do pedido](entitlement-management-process.md)
