---
title: Visualizar funções de administrador do Azure com permissões personalizáveis-Azure Active Directory | Microsoft Docs
description: Visualize funções personalizadas do Azure AD para delegar o gerenciamento de identidades. Gerencie funções do Azure no portal do Azure, PowerShell ou API do Graph.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779392"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizadas no Azure Active Directory (versão prévia)

Este artigo descreve como entender o novo RBAC personalizado (controle de acesso baseado em funções) e escopos de recursos no Azure Active Directory (Azure AD). As funções RBAC personalizadas superfícies as permissões subjacentes das [funções internas](directory-assign-admin-roles.md) , para que você possa criar e organizar suas próprias funções personalizadas. Os escopos de recursos fornecem uma maneira de atribuir a função personalizada para gerenciar alguns recursos (por exemplo, um aplicativo) sem conceder acesso a todos os recursos (todos os aplicativos).

A concessão de permissão usando funções RBAC personalizadas é um processo de duas etapas. Primeiro, você cria uma definição de função personalizada e adiciona permissões a ela na lista predefinida. Essas são as mesmas permissões usadas nas funções internas. Depois de criar sua função, atribua-a a alguém criando uma atribuição de função. Esse processo de duas etapas permite criar uma função e atribuí-la muitas vezes em escopos diferentes. Uma função personalizada pode ser atribuída no escopo do diretório ou pode ser atribuída em um escopo de objeto. Um exemplo de escopo de objeto seria um único aplicativo. Dessa forma, a mesma função pode ser atribuída ao Sally em todos os aplicativos no diretório e, em seguida, Naveen apenas o aplicativo contoso expense Reports.

Essa primeira versão das funções RBAC personalizadas inclui a capacidade de criar uma função para atribuir permissões para gerenciar registros de aplicativo. Ao longo do tempo, permissões adicionais para recursos da organização, como aplicativos empresariais, usuários e dispositivos, serão adicionadas.

Recursos de visualização:

- Atualizações da interface do usuário do portal para criar e gerenciar funções personalizadas e atribuí-las a usuários em escopo de toda a organização
- Um módulo do PowerShell de visualização com novos cmdlets para:
  - Criar e gerenciar funções personalizadas
  - Atribuir funções personalizadas com um escopo de registro em toda a organização ou por aplicativo
  - Atribuir funções internas em escopo de toda a organização (paridade com cmdlets de GA)
  - Suporte ao API do Graph do Azure AD

O controle de acesso baseado em função do AD do Azure é um recurso de visualização pública do Azure AD e está disponível com qualquer plano de licença pago do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Entender o controle de acesso baseado em função do Azure AD

O controle de acesso baseado em função do AD do Azure permite que você atribua funções que são personalizadas para permitir ações permitidas em apenas um único tipo de recurso do Azure AD. O acesso baseado em função do Azure AD opera em conceitos semelhantes ao controle de acesso baseado em função do Azure ([RBAC](../../role-based-access-control/overview.md) do Azure para acesso a recursos do Azure, mas o controle de acesso baseado em função do Azure AD é baseado em Microsoft Graph e o RBAC do Azure é baseado em Azure Resource Manager. No entanto, ambos os sistemas baseiam suas funções em atribuições de função.

### <a name="role-assignments"></a>Atribuições de funções

A maneira de controlar o acesso usando o controle de acesso baseado em função do AD do Azure é criar atribuições de **função**, que são usadas para impor permissões. Uma atribuição de função consiste em três elementos:

- Principal de segurança
- Definição de função
- Escopo de recurso

O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função. Você pode [criar atribuições de função](roles-create-custom.md) usando o portal do Azure, o PowerShell do Azure AD e o API do Graph. Você pode [Exibir separadamente as atribuições para uma função personalizada](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green recebeu a função de [administrador de aplicativos](directory-assign-admin-roles.md#application-administrator) no escopo do aplicativo Salesforce. Chris não tem acesso para gerenciar qualquer outro aplicativo, a menos que eles façam parte de uma atribuição de função diferente.

![A atribuição de função é como as permissões são impostas e tem três partes](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de segurança

Uma entidade de segurança representa o usuário ou a entidade de serviço que receberá acesso aos recursos do Azure AD. Um *usuário* é um indivíduo que tem um perfil de usuário no Azure Active Directory. Uma *entidade de serviço* é uma identidade de segurança usada por aplicativos ou serviços para acessar recursos específicos do Azure AD.

Uma entidade de segurança é semelhante a uma identidade de usuário, pois representa um nome de usuários e senha ou certificado, mas para um aplicativo ou serviço em vez de um usuário.

### <a name="role"></a>Role

Uma definição de função, ou função, é uma coleção de permissões. Uma definição de função lista as operações que podem ser executadas nos recursos do Azure AD, como criar, ler, atualizar e excluir. Há dois tipos de funções no Azure AD:

- Funções internas criadas pela Microsoft que não podem ser alteradas. A função interna de administrador global tem todas as permissões em todos os recursos do Azure AD.
- Funções personalizadas criadas e gerenciadas pela sua organização.

### <a name="scope"></a>Scope

Um escopo é a restrição de ações permitidas para um recurso específico do Azure AD. Ao atribuir uma função, você pode especificar um escopo que limita as ações permitidas do administrador a um recurso específico. Por exemplo, se você quiser conceder a um desenvolvedor uma função personalizada, mas apenas para gerenciar um registro de aplicativo específico, você poderá incluir o registro de aplicativo específico como um escopo na atribuição de função.

  > [!Note]
  > As funções personalizadas podem ser atribuídas no escopo do diretório e no escopo do recurso. Eles ainda não podem ser atribuídos no escopo da unidade administrativa.
  > Funções internas podem ser atribuídas no escopo do diretório e, em alguns casos, escopo da unidade administrativa. Eles ainda não podem ser atribuídos no escopo do objeto.

## <a name="required-license-plan"></a>Plano de licença necessário

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Criar atribuições de função personalizadas usando [o portal do Azure, o PowerShell do Azure AD e o API do Graph](roles-create-custom.md)
- [Exibir as atribuições para uma função personalizada](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
