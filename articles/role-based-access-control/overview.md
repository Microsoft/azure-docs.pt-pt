---
title: O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?
description: Obtenha uma visão geral do controlo de acesso baseado em funções Azure (Azure RBAC). Use atribuições de funções para controlar o acesso aos recursos da Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 45601e820bc03b263fbf664a43ce34266dc4a488
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171576"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?

A gestão de acesso para recursos na cloud é uma função crítica para qualquer organização que está a utilizar a cloud. O controlo de acesso baseado em funções (Azure RBAC) ajuda-o a gerir quem tem acesso aos recursos Azure, o que podem fazer com esses recursos e a que áreas têm acesso.

O Azure RBAC é um sistema de autorização construído na [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso fino dos recursos Azure.

Este vídeo fornece uma visão geral rápida do Azure RBAC.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>O que posso fazer com Azure RBAC?

Aqui estão alguns exemplos do que pode fazer com a Azure RBAC:

- Permitir a um utilizador gerir máquinas virtuais numa subscrição e a outro utilizador gerir redes virtuais
- Permitir a um grupo DBA gerir bases de dados SQL numa subscrição
- Permitir a um utilizador gerir todos os recursos num grupo de recursos, como máquinas virtuais, sites e sub-redes
- Permitir a uma aplicação aceder a todos os recursos num grupo de recursos

## <a name="how-azure-rbac-works"></a>Como funciona o Azure RBAC

A forma como controla o acesso aos recursos usando o Azure RBAC é criar atribuições de papéis. Este é um conceito chave para entender – é como as permissões são aplicadas. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito.

### <a name="security-principal"></a>Principal de segurança

Um *principal de segurança* é um objeto que representa um utilizador, grupo, diretor de serviço ou identidade gerida que está solicitando acesso aos recursos Azure.

![Principal de segurança para uma atribuição de função](./media/overview/rbac-security-principal.png)

- Utilizador – Um indivíduo que tem um perfil no Azure Active Directory. Também pode atribuir funções a utilizadores noutros inquilinos. Para obter informações sobre utilizadores noutras organizações, veja [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grupo – Um conjunto de utilizadores criado no Azure Active Directory. Quando atribui uma função a um grupo, todos os utilizadores nesse grupo têm essa função. 
- Principal de serviço – Uma identidade de segurança utilizada por aplicações ou serviços para aceder a recursos específicos do Azure. Pode considerá-lo como uma *identidade de utilizador* (nome de utilizador e palavra-passe ou certificado) para uma aplicação.
- Identidade gerida - Uma identidade no Azure Ative Directory que é gerida automaticamente pela Azure. Normalmente, utiliza [identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) ao desenvolver aplicações em nuvem para gerir as credenciais para autenticação nos serviços Azure.

### <a name="role-definition"></a>Definição de função

Uma *definição de função* é uma coleção de permissões. Normalmente chama-se *um papel.* Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. As funções podem ser de nível elevado, como proprietário, ou específicas, como leitor de máquina virtual.

![Definição de função para atribuição de função](./media/overview/rbac-role-definition.png)

O Azure inclui vários [papéis incorporados](built-in-roles.md) que podes usar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

- [Proprietário](built-in-roles.md#owner) - Tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outros.
- [Contribuinte](built-in-roles.md#contributor) - Pode criar e gerir todos os tipos de recursos Azure, mas não pode conceder acesso a outros.
- [Leitor](built-in-roles.md#reader) - Pode ver os recursos Azure existentes.
- [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) - Permite-lhe gerir o acesso do utilizador aos recursos do Azure.

As restantes funções incorporadas permitem a gestão de recursos específicos do Azure. Por exemplo, a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite a um utilizador criar e gerir máquinas virtuais. Se as funções incorporadas não corresponderem às necessidades específicas da sua organização, pode criar os seus [próprios papéis personalizados Azure.](custom-roles.md)

Este vídeo fornece uma visão geral rápida de papéis incorporados e papéis personalizados.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

O Azure tem operações de dados que lhe permitem conceder acesso a dados dentro de um objeto. Por exemplo, se um utilizador tiver acesso a dados de leitura a uma conta de armazenamento, pode ler os blobs ou as mensagens nessa conta de armazenamento. Para obter mais informações, consulte [as definições de funções do Azure.](role-definitions.md)

### <a name="scope"></a>Âmbito

*Âmbito* é o conjunto de recursos a que o acesso se aplica. Quando atribui uma função, pode limitar ainda mais as ações permitidas ao definir um âmbito. Isto é útil se quiser fazer de alguém um [Colaborador do Website,](built-in-roles.md#website-contributor)mas apenas para um grupo de recursos.

Em Azure, pode especificar um âmbito a vários níveis: [grupo de gestão,](../governance/management-groups/overview.md)subscrição, grupo de recursos ou recurso. Os âmbitos são estruturados numa relação de principal-subordinado.

![Âmbito de uma atribuição de função](./media/overview/rbac-scope.png)

Quando concede acesso a um âmbito principal, os âmbitos secundários herdam essas permissões. Por exemplo:

- Se atribuir a função [Proprietário](built-in-roles.md#owner) a um utilizador no âmbito de grupo de gestão, esse utilizador pode gerir tudo em todas as subscrições no grupo de gestão.
- Se atribuir a função [Leitor](built-in-roles.md#reader) a um grupo no âmbito da subscrição, os membros desse grupo pode ver cada grupo de recursos e recurso na subscrição.
- Se atribuir a função [Contribuidor](built-in-roles.md#contributor) a uma aplicação no âmbito do grupo de recursos, pode gerir recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na subscrição.

### <a name="role-assignments"></a>Atribuições de funções

Uma *atribuição de funções* é o processo de anexação de uma definição de papel a um utilizador, grupo, principal de serviço ou identidade gerida num determinado âmbito para a concessão de acesso. O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função.

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, foi atribuída a função [Contribuidor](built-in-roles.md#contributor) ao grupo Marketing para o grupo de recursos de vendas farmacêuticas. Isto significa que os utilizadores no grupo Marketing podem criar ou gerir qualquer recurso do Azure no grupo de recursos de vendas farmacêuticas. Os utilizadores do grupo Marketing não têm acesso aos recursos fora o grupo de recursos de vendas farmacêuticas, a menos que façam parte de outra atribuição de função.

![Atribuição de função para controlar o acesso](./media/overview/rbac-overview.png)

Pode criar atribuições de funções no portal do Azure, CLI do Azure, Azure PowerShell, SDKs do Azure ou APIs REST. Pode ter até **2000** atribuições de funções em cada subscrição. Este limite inclui atribuições de funções nos âmbitos de subscrição, grupo de recursos e recursos. Você pode ter até **500** atribuições de papel em cada grupo de gestão. Para criar e remover atribuições de funções, precisa de ter a permissão `Microsoft.Authorization/roleAssignments/*`. Esta permissão é concedida através das funções [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso de Utilizador](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Atribuições múltiplas de papéis

Então, o que acontece se tiver várias atribuições de papéis sobrepostos? O Azure RBAC é um modelo aditivo, por isso as suas permissões eficazes são a soma das suas atribuições de papel. Considere o exemplo a seguir quando um utilizador recebe o papel de Contribuinte no âmbito de subscrição e a função Reader num grupo de recursos. A soma das permissões do Contribuinte e das permissões do Leitor é efetivamente a função contribuinte para o grupo de recursos. Portanto, neste caso, a atribuição de funções reader não tem impacto.

![Atribuições múltiplas de papéis](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Atribuições de negação

Anteriormente, o Azure RBAC era um modelo só de admissão sem qualquer negação, mas agora o Azure RBAC suporta a negação de atribuições de forma limitada. Semelhante a uma atribuição de funções, uma *atribuição de negação* anexa um conjunto de ações de negação a um utilizador, grupo, principal de serviço ou identidade gerida num determinado âmbito para o propósito de negar o acesso. Uma atribuição de funções define um conjunto de ações que são *permitidas,* enquanto uma atribuição de negação define um conjunto de ações que não são *permitidas*. Por outras palavras, as atribuições de negação impedem os utilizadores de executarem ações especificadas, mesmo que uma atribuição de função lhes conceda acesso. As atribuições de negação têm precedência sobre as atribuições de funções. Para obter mais informações, consulte [Understand Azure negar atribuições.](deny-assignments.md)

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Como o Azure RBAC determina se um utilizador tem acesso a um recurso

Seguem-se os passos de alto nível que o Azure RBAC utiliza para determinar se tem acesso a um recurso no plano de gestão. Isto pode ser útil para compreender se estiver a tentar resolver um problema de acesso.

1. Um utilizador (ou principal de serviço) obtém um token para o Azure Resource Manager.

    O token inclui as associações a grupos do utilizador (incluindo as associações de grupo transitivas).

1. O utilizador faz uma chamada da API de REST para o Azure Resource Manager com o token anexado.

1. O Azure Resource Manager obtém todas as atribuições de função e de negação que se aplicam ao recurso no qual a ação é executada.

1. O Azure Resource Manager restringe as atribuições de funções que se aplicam a este utilizador ou ao respetivo grupo e determina que funções o utilizador tem para este recurso.

1. O Azure Resource Manager determina se a ação na chamada de API está incluída nas funções que utilizador tem para este recurso.

1. Se o utilizador não tiver um papel na ação no âmbito solicitado, o acesso não é concedido. Caso contrário, o Azure Resource Manager verifica se uma atribuição de negação se aplica.

1. Se uma atribuição de negação se aplicar, o acesso é bloqueado. Caso contrário, o acesso é concedido.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Próximos passos

- [Quickstart: Ver o acesso que um utilizador tem aos recursos da Azure](check-access.md)
- [Adicione ou remova atribuições de funções Azure usando o portal Azure](role-assignments-portal.md)
- [Compreender as diferentes funções](rbac-and-directory-admin-roles.md)
- [Cloud Adopt Framework: Gestão do acesso a recursos em Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
