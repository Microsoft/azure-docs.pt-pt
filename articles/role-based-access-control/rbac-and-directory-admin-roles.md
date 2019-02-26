---
title: Funções de administrador de subscrição clássica, funções RBAC do Azure e funções de administrador do Azure AD | Microsoft Docs
description: 'Descreve as diferentes funções no Azure: funções de administrador de subscrição clássica, funções de controlo de acesso baseado em funções (RBAC) do Azure e funções de administrador do Azure Active Directory (Azure AD)'
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
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: c53b9f13c365a5ace227f792f5b0c80ae8fac6ff
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807359"
---
# <a name="classic-subscription-administrator-roles-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Funções de administrador de subscrição clássica, funções RBAC do Azure e funções de administrador do Azure AD

Se não estiver familiarizado com o Azure, pode ser um pouco difícil compreender todas as diferentes funções. Este artigo ajuda a explicar as funções seguintes e quando utilizar cada uma:
- Funções de administrador de subscrição clássica
- Funções de controlo de acesso baseado em funções (RBAC) do Azure
- Funções de administrador do Azure Active Directory (Azure AD)

## <a name="how-the-roles-are-related"></a>De que forma estão relacionadas as funções

Para compreender melhor as funções no Azure, ajuda conhecer alguma da história. Quando o Azure foi inicialmente lançado, o acesso aos recursos foi gerido com apenas três funções de administrador: Administrador de conta, administrador de serviços e Coadministrador. Mais tarde, foi adicionado o controlo de acesso baseado em funções (RBAC) para os recursos do Azure. O RBAC do Azure é um sistema de autorização mais recente que fornece gestão pormenorizada de acesso para recursos do Azure. O RBAC inclui muitas funções incorporadas, pode ser atribuído a âmbitos diferentes e permite-lhe criar as suas próprias funções personalizadas. Para gerir recursos no Azure AD, como utilizadores, grupos e domínios, existem várias funções de administrador do Azure AD.

O diagrama seguinte é uma visão geral da forma como estão relacionadas as funções de administrador de subscrição clássica, as funções de RBAC do Azure e as funções de administrador do Azure AD.

![As diferentes funções no Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Administrador de Conta, Administrador de Serviços e Coadministrador são as três funções de administrador de subscrição clássica no Azure. Os administradores de subscrição clássica têm acesso total à subscrição do Azure. Podem gerir recursos com o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implementação clássica. A conta utilizada para se inscrever no Azure é definida automaticamente como Administrador de Conta e Administrador de Serviços. Em seguida, podem ser adicionados outros Coadministradores. O Administrador de Serviços e os Coadministradores têm o acesso equivalente de utilizadores com a função Proprietário atribuída (uma função RBAC do Azure) no âmbito da subscrição. A tabela seguinte descreve as diferenças entre estas três funções administrativas de subscrição clássica.

| Administrador de subscrição clássica | Limite | Permissões | Notas |
| --- | --- | --- | --- |
| Administrador de Conta | 1 por conta do Azure | <ul><li>Aceda ao [Centro de Contas do Azure](https://account.azure.com/Subscriptions)</li><li>Gerir todas as subscrições numa conta</li><li>Criar novas subscrições</li><li>Cancelar subscrições</li><li>Alterar a faturação de uma subscrição</li><li>Alterar o Administrador de Serviços</li></ul> | Conceitualmente, o proprietário de faturação da subscrição.<br>O Administrador de Conta não tem acesso ao portal do Azure. |
| Administrador de Serviços | 1 por subscrição do Azure | <ul><li>Gerir os serviços no [portal do Azure](https://portal.azure.com)</li><li>Atribuir utilizadores à função Coadministrador</li></ul> | Por predefinição, para uma nova subscrição, o Administrador de Conta também é o Administrador de Serviços.<br>O Administrador de Serviços tem o acesso equivalente de um utilizador com a função Proprietário atribuída no âmbito da subscrição.<br>O Administrador de Serviços tem acesso total ao portal do Azure. |
| Coadministrador | 200 por subscrição | <ul><li>Tem os mesmos privilégios de acesso do Administrador de Serviços, mas não pode alterar a associação de subscrições a diretórios do Azure</li><li>Atribuir utilizadores à função Coadministrador, mas não pode alterar o Administrador de Serviços</li></ul> | O Coadministrador tem o acesso equivalente de um utilizador com a função Proprietário atribuída no âmbito da subscrição. |

No portal do Azure, pode gerir os coadministradores ou ver o administrador de serviços utilizando a **administradores clássico** separador.

![Administradores de subscrição clássica do Azure no portal do Azure](./media/rbac-and-directory-admin-roles/classic-administrators.png)

No portal do Azure, pode ver ou alterar o administrador de serviço ou ver o administrador de conta no painel de propriedades da sua subscrição.

![Administrador de Conta e Administrador de Serviços no portal do Azure](./media/rbac-and-directory-admin-roles/account-admin.png)

Para obter mais informações, consulte [os administradores de subscrição clássica do Azure](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Conta e subscrições do Azure

Uma conta do Azure representa uma relação de faturação. Uma conta do Azure é uma identidade de utilizador, uma ou mais subscrições do Azure e um conjunto associado de recursos do Azure. A pessoa que cria a conta é o Administrador de Conta de todas as subscrições criadas nessa conta. Essa pessoa também é o Administrador de Serviços predefinido da subscrição.

As subscrições do Azure ajudam a organizar o acesso aos recursos do Azure. Também ajudam a controlar de que forma é que a utilização dos recursos é comunicada, faturada e paga. Cada subscrição pode ter uma configuração de faturação e pagamento diferente, para que possa ter subscrições e planos diferentes por escritório, departamento, projeto e assim sucessivamente. Cada serviço pertence a uma subscrição e o ID de subscrição pode ser necessário para operações de programação.

As contas e subscrições são geridas no [Centro de Contas do Azure](https://account.azure.com/Subscriptions).

## <a name="azure-rbac-roles"></a>Funções RBAC do Azure

O RBAC do Azure é um sistema de autorização criado com base no [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) que fornece gestão pormenorizada de acesso para recursos do Azure, como computação e armazenamento. O RBAC do Azure inclui mais de 70 funções incorporadas. Existem quatro funções RBAC fundamentais. As três primeiras aplicam-se a todos os tipos de recursos:

| Função RBAC do Azure | Permissões | Notas |
| --- | --- | --- |
| [Proprietário](built-in-roles.md#owner) | <ul><li>Acesso total a todos os recursos</li><li>Delegar o acesso a outras pessoas</li></ul> | É atribuída a função Proprietário ao Administrador de Serviços e aos Coadministradores no âmbito da subscrição<br>Aplica-se a todos os tipos de recursos. |
| [Contribuidor](built-in-roles.md#contributor) | <ul><li>Criar e gerir todos os tipos de recursos do Azure</li><li>Não é possível conceder acesso a outras pessoas</li></ul> | Aplica-se a todos os tipos de recursos. |
| [Leitor](built-in-roles.md#reader) | <ul><li>Ver os recursos do Azure</li></ul> | Aplica-se a todos os tipos de recursos. |
| [Administrador de Acesso de Utilizador](built-in-roles.md#user-access-administrator) | <ul><li>Gerir o acesso de utilizador aos recursos do Azure</li></ul> |  |

As restantes funções incorporadas permitem a gestão de recursos específicos do Azure. Por exemplo, a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite ao utilizador criar e gerir máquinas virtuais. Para obter uma lista de todas as funções incorporadas, consulte [funções incorporadas para recursos do Azure](built-in-roles.md).

Apenas o portal do Azure e as APIs do Azure Resource Manager suportam o RBAC. Os utilizadores, grupos e aplicações com funções RBAC atribuídas não podem utilizar as [APIs do modelo de implementação clássica do Azure](../azure-resource-manager/resource-manager-deployment-model.md).

No portal do Azure, as atribuições de funções através do RBAC aparecem no painel **Controlo de acesso (IAM)**. Neste painel pode encontradas em todo o portal, como grupos de gestão, subscrições, grupos de recursos e vários recursos.

![Painel de controlo de acesso (IAM) no portal do Azure](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Quando clica no **funções** guia, verá a lista de funções incorporadas e personalizadas.

![Funções incorporadas no portal do Azure](./media/rbac-and-directory-admin-roles/roles-list.png)

Para obter mais informações, consulte [gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure](role-assignments-portal.md).

## <a name="azure-ad-administrator-roles"></a>Funções de administrador do Azure AD

As funções Administrador do AD Azure são utilizadas para gerir os recursos do Azure AD num diretório, como criar ou editar utilizadores, atribuir funções administrativas a outras pessoas, repor palavras-passe de utilizador, gerir licenças de utilizador e gerir domínios. A tabela seguinte descreve algumas das funções de administrador do Azure AD mais importantes.

| Função de administrador do Azure AD | Permissões | Notas |
| --- | --- | --- |
| [Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) | <ul><li>Gerir o acesso a todas as funcionalidades administrativas no Azure Active Directory, bem como serviços com federação para o Azure Active Directory</li><li>Atribuir funções de administrador a outras pessoas</li><li>Repor a palavra-passe para qualquer utilizador e todos os outros administradores</li></ul> | A pessoa que se inscreve no inquilino do Azure Active Directory torna-se um Administrador Global. |
| [Administrador de Utilizadores](../active-directory/users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) | <ul><li>Criar e gerir todos os aspetos de utilizadores e grupos</li><li>Gerir pedidos de suporte</li><li>Monitorizar o estado de funcionamento do serviço</li><li>Alterar palavras-passe para utilizadores, administradores de suporte técnico e outros Administradores de Utilizadores</li></ul> |  |
| [Administrador de Faturação](../active-directory/users-groups-roles/directory-assign-admin-roles.md#billing-administrator) | <ul><li>Efetuar compras</li><li>Gerir subscrições</li><li>Gerir pedidos de suporte</li><li>Monitoriza o estado de funcionamento do serviço</li></ul> |  |

No portal do Azure, pode ver a lista de funções de administrador do Azure AD no painel **Funções e administradores**. Para obter uma lista de todas as funções de administrador do Azure AD, consulte [permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

![Funções de administrador do Azure AD no portal do Azure](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Diferenças entre funções RBAC do Azure e funções de administrador do Azure AD

A um nível elevado, as funções RBAC do Azure controlam as permissões para gerir recursos do Azure, enquanto que as funções de administrador do Azure AD controlam as permissões para gerir os recursos do Azure Active Directory. A tabela seguinte compara algumas das diferenças.

| Funções RBAC do Azure | Funções de administrador do Azure AD |
| --- | --- |
| Gerir o acesso aos recursos do Azure | Gerir o acesso aos recursos do Azure Active Directory |
| Suporta funções personalizadas | Não pode criar as suas próprias funções |
| O âmbito pode ser especificado em vários níveis (grupo de gestão, subscrição, grupo de recursos, recurso) | O âmbito está ao nível do inquilino |
| As informações das funções podem ser acedidas no portal do Azure, CLI do Azure, Azure PowerShell, modelos do Azure Resource Manager, API REST | As informações das funções podem ser acedidas no portal de administração do Azure, portal de administração do Office 365, Microsoft Graph, AzureAD PowerShell |

### <a name="do-azure-rbac-roles-and--azure-ad-administrator-roles-overlap"></a>As funções RBAC do Azure e as funções de administrador do Azure AD sobrepõem-se?

Por predefinição, as funções RBAC do Azure e as funções de administrador do Azure AD não se expandem ao Azure e ao Azure AD. No entanto, se um Administrador Global elevar o acesso ao selecionar a opção **O administrador Global pode gerir as Subscrições do Azure e os Grupos de Gestão** no portal do Azure, será concedida ao Administrador Global a função [Administrador de Acesso de Utilizador](built-in-roles.md#user-access-administrator) (uma função RBAC) em todas as subscrições de um inquilino específico. A função Administrador de Acesso de Utilizador permite ao utilizador conceder a outros utilizadores o acesso aos recursos do Azure. Esta opção pode ser útil para recuperar o acesso a uma subscrição. Para obter mais informações, veja [Elevar o acesso de um administrador do Azure AD](elevate-access-global-admin.md).

Várias funções de administrador do Azure AD expandem-se ao Azure AD e ao Microsoft Office 365, como as funções Administrador Global e Administrador de Utilizadores. Por exemplo, se for um membro da função Administrador Global, tem capacidades de administrador global no Azure AD e no Office 365, como fazer alterações ao Microsoft Exchange e ao Microsoft SharePoint. No entanto, por predefinição, o Administrador Global não tem acesso aos recursos do Azure.

![RBAC do Azure em comparação com as funções de administrador do Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Passos Seguintes

- [O que é o controlo de acesso baseado em funções (RBAC) para recursos do Azure?](overview.md)
- [Permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)
- [Administradores de subscrição clássica do Azure](classic-administrators.md)
