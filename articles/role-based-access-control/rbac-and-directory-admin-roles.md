---
title: Funções clássicas de administrador de subscrição, funções Azure e Azure AD
description: Descreve as diferentes funções em funções de administrador de subscrição clássico, funções Azure e Azure Ative Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 70baddf86207c490d3b85e0f584525592f8a7ad7
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735822"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Funções clássicas de administrador de subscrição, funções Azure e Azure AD

Se não estiver familiarizado com o Azure, pode ser um pouco difícil compreender todas as diferentes funções. Este artigo ajuda a explicar as funções seguintes e quando utilizar cada uma:
- Funções de administrador de subscrição clássica
- Funções do Azure
- Funções de Diretório Ativo Azure (Azure AD)

## <a name="how-the-roles-are-related"></a>De que forma estão relacionadas as funções

Para compreender melhor as funções no Azure, ajuda conhecer alguma da história. Quando o Azure foi lançado inicialmente, o acesso aos recursos era gerido com apenas três funções de administrador: Administrador de Conta, Administrador de Serviços e Coadministrador. Mais tarde, foi adicionado o controlo de acesso baseado em funções azure (Azure RBAC). O RBAC do Azure é um sistema de autorização mais recente que fornece gestão pormenorizada de acesso para recursos do Azure. O Azure RBAC inclui muitas funções incorporadas, pode ser atribuída em diferentes âmbitos, e permite-lhe criar as suas próprias funções personalizadas. Para gerir recursos em Azure AD, como utilizadores, grupos e domínios, existem várias funções de AD Azure.

O diagrama a seguir é uma visão de alto nível de como as funções clássicas do administrador de subscrição, papéis Azure e Azure AD estão relacionados.

![As diferentes funções no Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Administrador de Conta, Administrador de Serviços e Coadministrador são as três funções de administrador de subscrição clássica no Azure. Os administradores de subscrição clássica têm acesso total à subscrição do Azure. Podem gerir recursos com o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implementação clássica. A conta utilizada para se inscrever no Azure é definida automaticamente como Administrador de Conta e Administrador de Serviços. Em seguida, podem ser adicionados outros Coadministradores. O Administrador de Serviço e os Coadministradores têm o acesso equivalente aos utilizadores a quem foi atribuída a função Proprietário (uma função Azure) no âmbito da subscrição. A tabela seguinte descreve as diferenças entre estas três funções administrativas de subscrição clássica.

| Administrador de subscrição clássica | Limite | Permissões | Notas |
| --- | --- | --- | --- |
| Administrador de Conta | 1 por conta do Azure | <ul><li>Aceda ao [Centro de Contas do Azure](https://account.azure.com/Subscriptions)</li><li>Gerir todas as subscrições numa conta</li><li>Criar novas subscrições</li><li>Cancelar subscrições</li><li>Alterar a faturação de uma subscrição</li><li>Alterar o Administrador de Serviços</li></ul> | Conceitualmente, o proprietário de faturação da subscrição.<br>O Administrador de Conta não tem acesso ao portal do Azure. |
| Administrador de Serviços | 1 por subscrição do Azure | <ul><li>Gerir os serviços no [portal do Azure](https://portal.azure.com)</li><li>Cancelar a subscrição</li><li>Atribuir utilizadores à função Coadministrador</li></ul> | Por predefinição, para uma nova subscrição, o Administrador de Conta também é o Administrador de Serviços.<br>O Administrador de Serviços tem o acesso equivalente de um utilizador com a função Proprietário atribuída no âmbito da subscrição.<br>O Administrador de Serviços tem acesso total ao portal do Azure. |
| Coadministrador | 200 por subscrição | <ul><li>Tem os mesmos privilégios de acesso do Administrador de Serviços, mas não pode alterar a associação de subscrições a diretórios do Azure</li><li>Atribuir utilizadores à função Coadministrador, mas não pode alterar o Administrador de Serviços</li></ul> | O Coadministrador tem o acesso equivalente de um utilizador com a função Proprietário atribuída no âmbito da subscrição. |

No portal Azure, pode gerir Coadministradores ou ver o Administrador de Serviço utilizando o separador **de administradores Clássicos.**

![Administradores de subscrição clássicos do Azure no portal Azure](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

No portal Azure, pode visualizar ou alterar o Administrador de Serviço ou ver o Administrador de Conta na lâmina de propriedades da sua subscrição.

![Administrador de Conta e Administrador de Serviços no portal do Azure](./media/rbac-and-directory-admin-roles/account-admin.png)

Para mais informações, consulte [os administradores de subscrição clássicos do Azure.](classic-administrators.md)

### <a name="azure-account-and-azure-subscriptions"></a>Conta e subscrições do Azure

Uma conta do Azure representa uma relação de faturação. Uma conta do Azure é uma identidade de utilizador, uma ou mais subscrições do Azure e um conjunto associado de recursos do Azure. A pessoa que cria a conta é o Administrador de Conta de todas as subscrições criadas nessa conta. Essa pessoa também é o Administrador de Serviços predefinido da subscrição.

As subscrições do Azure ajudam a organizar o acesso aos recursos do Azure. Também ajudam a controlar de que forma é que a utilização dos recursos é comunicada, faturada e paga. Cada subscrição pode ter uma configuração de faturação e pagamento diferente, para que possa ter subscrições e planos diferentes por escritório, departamento, projeto e assim sucessivamente. Cada serviço pertence a uma subscrição e o ID de subscrição pode ser necessário para operações de programação.

Cada subscrição está associada a um diretório Azure AD. Para encontrar o diretório a subscrição está associada, abra **assinaturas** no portal Azure e, em seguida, selecione uma subscrição para ver o diretório.

As contas e subscrições são geridas no [Centro de Contas do Azure](https://account.azure.com/Subscriptions).

## <a name="azure-roles"></a>Funções do Azure

O RBAC do Azure é um sistema de autorização criado com base no [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece gestão pormenorizada de acesso para recursos do Azure, como computação e armazenamento. O RBAC do Azure inclui mais de 70 funções incorporadas. Há quatro papéis fundamentais do Azure. As três primeiras aplicam-se a todos os tipos de recursos:

| Papel azure | Permissões | Notas |
| --- | --- | --- |
| [Proprietário](built-in-roles.md#owner) | <ul><li>Acesso total a todos os recursos</li><li>Delegar o acesso a outras pessoas</li></ul> | É atribuída a função Proprietário ao Administrador de Serviços e aos Coadministradores no âmbito da subscrição<br>Aplica-se a todos os tipos de recursos. |
| [Contribuinte](built-in-roles.md#contributor) | <ul><li>Criar e gerir todos os tipos de recursos do Azure</li><li>Criar um novo inquilino no Diretório Ativo azure</li><li>Não é possível conceder acesso a outras pessoas</li></ul> | Aplica-se a todos os tipos de recursos. |
| [Leitor](built-in-roles.md#reader) | <ul><li>Ver os recursos do Azure</li></ul> | Aplica-se a todos os tipos de recursos. |
| [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator) | <ul><li>Gerir o acesso de utilizador aos recursos do Azure</li></ul> |  |

As restantes funções incorporadas permitem a gestão de recursos específicos do Azure. Por exemplo, a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite ao utilizador criar e gerir máquinas virtuais. Para obter uma lista de todos os papéis incorporados, consulte [os papéis integrados de Azure.](built-in-roles.md)

Apenas o portal Azure e o Gestor de Recursos Azure APIs suportam o Azure RBAC. Os utilizadores, grupos e aplicações que lhe são atribuídas funções Azure não podem utilizar o [modelo de implantação clássico Do Azure APIs](../azure-resource-manager/management/deployment-models.md).

No portal Azure, as atribuições de funções utilizando o Azure RBAC aparecem na lâmina de controlo de **acesso (IAM).** Esta lâmina pode ser encontrada em todo o portal, tais como grupos de gestão, subscrições, grupos de recursos e vários recursos.

![Painel de controlo de acesso (IAM) no portal do Azure](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Quando clicar no separador **Roles,** verá a lista de papéis incorporados e personalizados.

![Funções incorporadas no portal do Azure](./media/rbac-and-directory-admin-roles/roles-list.png)

Para mais informações, consulte Adicionar ou remover atribuições de [funções Azure utilizando o portal Azure](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Funções do Azure AD

As funções azure AD são usadas para gerir os recursos da AD Azure num diretório como criar ou editar utilizadores, atribuir funções administrativas a outros, redefinir as palavras-passe dos utilizadores, gerir licenças de utilizador e gerir domínios. A tabela que se segue descreve algumas das funções mais importantes da AD Azure.

| Papel da AD Azure | Permissões | Notas |
| --- | --- | --- |
| [Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) | <ul><li>Gerir o acesso a todas as funcionalidades administrativas no Azure Active Directory, bem como serviços com federação para o Azure Active Directory</li><li>Atribuir funções de administrador a outras pessoas</li><li>Repor a palavra-passe para qualquer utilizador e todos os outros administradores</li></ul> | A pessoa que se inscreve no inquilino do Azure Active Directory torna-se um Administrador Global. |
| [Administrador de Utilizadores](../active-directory/users-groups-roles/directory-assign-admin-roles.md#user-administrator) | <ul><li>Criar e gerir todos os aspetos de utilizadores e grupos</li><li>Gerir pedidos de suporte</li><li>Monitorizar o estado de funcionamento do serviço</li><li>Alterar palavras-passe para utilizadores, administradores de suporte técnico e outros Administradores de Utilizadores</li></ul> |  |
| [Administrador de Faturação](../active-directory/users-groups-roles/directory-assign-admin-roles.md#billing-administrator) | <ul><li>Efetuar compras</li><li>Gerir subscrições</li><li>Gerir pedidos de suporte</li><li>Monitoriza o estado de funcionamento do serviço</li></ul> |  |

No portal Azure, pode ver a lista de funções da Azure AD na lâmina **de Funções e administradores.** Para obter uma lista de todas as funções da AD Azure, consulte [permissões de funções de administrador no Diretório Ativo azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

![Funções da AD Azure no portal Azure](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Diferenças entre papéis Azure e Azure AD

A um nível elevado, as funções azure controlam permissões para gerir os recursos do Azure, enquanto as funções da Azure AD controlam permissões para gerir os recursos do Azure Ative Directory. A tabela seguinte compara algumas das diferenças.

| Funções do Azure | Funções do Azure AD |
| --- | --- |
| Gerir o acesso aos recursos do Azure | Gerir o acesso aos recursos do Azure Active Directory |
| Suporta funções personalizadas | Suporta funções personalizadas |
| O âmbito pode ser especificado em vários níveis (grupo de gestão, subscrição, grupo de recursos, recurso) | O âmbito está ao nível do inquilino |
| As informações das funções podem ser acedidas no portal do Azure, CLI do Azure, Azure PowerShell, modelos do Azure Resource Manager, API REST | As informações sobre as funções podem ser acedidas no portal de administração do Azure, no microsoft 365 centro de administração, no Microsoft Graph, no AzureAD PowerShell |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>As funções de Azure e Azure AD sobrepõem-se?

Por predefinição, as funções Azure e Azure AD não abrangem a Azure e a Azure AD. No entanto, se um Administrador Global elevar o seu acesso ao escolher o administrador global pode gerir a switch **de Assinaturas e Grupos de Gestão Azure** no portal Azure, o Administrador Global receberá o papel de Administrador de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) (uma função Azure) em todas as subscrições de um determinado inquilino. A função Administrador de Acesso de Utilizador permite ao utilizador conceder a outros utilizadores o acesso aos recursos do Azure. Esta opção pode ser útil para recuperar o acesso a uma subscrição. Para mais informações, consulte [O acesso elevado para gerir todas as subscrições e grupos de gestão do Azure.](elevate-access-global-admin.md)

Várias funções de AD Azure abrangem a Azure AD e o Microsoft Office 365, tais como as funções de Administrador Global e Administrador de Utilizador. Por exemplo, se for um membro da função Administrador Global, tem capacidades de administrador global no Azure AD e no Office 365, como fazer alterações ao Microsoft Exchange e ao Microsoft SharePoint. No entanto, por predefinição, o Administrador Global não tem acesso aos recursos do Azure.

![Funções Azure RBAC versus Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Passos seguintes

- [O que é o controlo de acesso baseado em funções azure (Azure RBAC)?](overview.md)
- [Administrator role permissions in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) (Permissões de cargos de administrador no Azure Active Directory)
- [Administradores de subscrição clássicos do Azure](classic-administrators.md)
- [Cloud Adoption Framework: Gestão de acesso a recursos em Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
