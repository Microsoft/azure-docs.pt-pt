---
title: O que é o controlo de acesso baseado em funções (RBAC) dos recursos do Azure? | Microsoft Docs
description: Obtenha uma visão geral do controlo de acesso baseado em papéis (RBAC) para os recursos Azure. Utilize atribuições de funções para controlar o acesso aos recursos do Azure.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6e3313e3ae201d0b730d8582fed9659d89f0d0c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80063062"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>O que é o controlo de acesso baseado em funções (RBAC) dos recursos do Azure?

A gestão de acesso para recursos na cloud é uma função crítica para qualquer organização que está a utilizar a cloud. O controlo de acesso baseado em funções (RBAC) ajuda-o a gerir quem tem acesso aos recursos do Azure, o que fazem com esses recursos e a que áreas têm acesso.

O RBAC é um sistema de autorização construído no [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso de grãos finos de recursos Azure.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Seguem-se alguns exemplos do que pode fazer com o RBAC:

- Permitir a um utilizador gerir máquinas virtuais numa subscrição e a outro utilizador gerir redes virtuais
- Permitir a um grupo DBA gerir bases de dados SQL numa subscrição
- Permitir a um utilizador gerir todos os recursos num grupo de recursos, como máquinas virtuais, sites e sub-redes
- Permitir a uma aplicação aceder a todos os recursos num grupo de recursos

## <a name="best-practice-for-using-rbac"></a>Melhor prática para utilizar o RBAC

Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções. Em vez de dar a todas as pessoas permissões sem restrições na sua subscrição do Azure ou recursos, pode permitir apenas determinadas ações num âmbito específico.

Quando planear a estratégia de controlo de acesso, é boa prática conceder aos utilizadores o privilégio menor de que precisam para trabalhar. O diagrama seguinte mostra um padrão sugerido para utilizar o RBAC.

![RBAC e menor privilégio](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Como funciona o RBAC

A forma de controlar o acesso a recursos com o RBAC é criar atribuições de funções. Este é um conceito chave para entender – é como as permissões são aplicadas. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito.

### <a name="security-principal"></a>Principal de segurança

Um diretor de *segurança* é um objeto que representa um utilizador, grupo, diretor de serviço ou identidade gerida que está a solicitar o acesso aos recursos do Azure.

![Principal de segurança para uma atribuição de função](./media/overview/rbac-security-principal.png)

- Utilizador – Um indivíduo que tem um perfil no Azure Active Directory. Também pode atribuir funções a utilizadores noutros inquilinos. Para obter informações sobre utilizadores noutras organizações, veja [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grupo – Um conjunto de utilizadores criado no Azure Active Directory. Quando atribui uma função a um grupo, todos os utilizadores nesse grupo têm essa função. 
- Principal de serviço – Uma identidade de segurança utilizada por aplicações ou serviços para aceder a recursos específicos do Azure. Pode considerá-lo como uma *identidade de utilizador* (nome de utilizador e palavra-passe ou certificado) para uma aplicação.
- Identidade gerida - Uma identidade no Diretório Ativo Azure que é gerida automaticamente pelo Azure. Normalmente utiliza [identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) ao desenvolver aplicações em nuvem para gerir as credenciais para autenticação nos serviços Azure.

### <a name="role-definition"></a>Definição de função

Uma *definição de função* é uma coleção de permissões. Normalmente chama-se um *papel.* Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. As funções podem ser de nível elevado, como proprietário, ou específicas, como leitor de máquina virtual.

![Definição de função para atribuição de função](./media/overview/rbac-role-definition.png)

O Azure inclui várias [funções incorporadas](built-in-roles.md) que pode utilizar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

- [Proprietário](built-in-roles.md#owner) – Tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outras pessoas.
- [Colaborador](built-in-roles.md#contributor) - Pode criar e gerir todos os tipos de recursos Azure, mas não pode dar acesso a outros.
- [Leitor](built-in-roles.md#reader) – Pode ver os recursos do Azure existentes.
- [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator) – Permite gerir o acesso do utilizador aos recursos do Azure.

As restantes funções incorporadas permitem a gestão de recursos específicos do Azure. Por exemplo, a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite a um utilizador criar e gerir máquinas virtuais. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar as suas próprias [funções personalizadas para os recursos Azure.](custom-roles.md)

O Azure tem operações de dados que lhe permitem conceder acesso a dados dentro de um objeto. Por exemplo, se um utilizador tiver acesso a dados de leitura a uma conta de armazenamento, pode ler os blobs ou as mensagens nessa conta de armazenamento. Para mais informações, consulte Compreender as definições de [papéis para os recursos Do Azure.](role-definitions.md)

### <a name="scope"></a>Âmbito

*O âmbito* é o conjunto de recursos a que o acesso se aplica. Quando atribui uma função, pode limitar ainda mais as ações permitidas ao definir um âmbito. Isto é útil se quiser tornar alguém [Contribuidor de Site](built-in-roles.md#website-contributor), mas apenas para um grupo de recursos.

No Azure, pode especificar um âmbito a vários níveis: [grupo de gestão](../governance/management-groups/overview.md), subscrição, grupo de recursos ou recurso. Os âmbitos são estruturados numa relação de principal-subordinado.

![Âmbito de uma atribuição de função](./media/overview/rbac-scope.png)

Quando concede acesso a um âmbito principal, os âmbitos secundários herdam essas permissões. Por exemplo:

- Se atribuir a função [Proprietário](built-in-roles.md#owner) a um utilizador no âmbito de grupo de gestão, esse utilizador pode gerir tudo em todas as subscrições no grupo de gestão.
- Se atribuir a função [Leitor](built-in-roles.md#reader) a um grupo no âmbito da subscrição, os membros desse grupo pode ver cada grupo de recursos e recurso na subscrição.
- Se atribuir a função [Contribuidor](built-in-roles.md#contributor) a uma aplicação no âmbito do grupo de recursos, pode gerir recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na subscrição.

### <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de *funções* é o processo de anexação de uma definição de função a um utilizador, grupo, diretor de serviço ou identidade gerida num âmbito específico para efeitos de concessão de acesso. O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função.

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, foi atribuída a função [Contribuidor](built-in-roles.md#contributor) ao grupo Marketing para o grupo de recursos de vendas farmacêuticas. Isto significa que os utilizadores no grupo Marketing podem criar ou gerir qualquer recurso do Azure no grupo de recursos de vendas farmacêuticas. Os utilizadores do grupo Marketing não têm acesso aos recursos fora o grupo de recursos de vendas farmacêuticas, a menos que façam parte de outra atribuição de função.

![Atribuição de função para controlar o acesso](./media/overview/rbac-overview.png)

Pode criar atribuições de funções no portal do Azure, CLI do Azure, Azure PowerShell, SDKs do Azure ou APIs REST. Pode ter até **2000** atribuições de funções em cada subscrição e **500** atribuições de funções em cada grupo de gestão. Para criar e remover atribuições de funções, precisa de ter a permissão `Microsoft.Authorization/roleAssignments/*`. Esta permissão é concedida através das funções [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso de Utilizador](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Atribuições de múltiplos papéis

O que acontece se tiver várias missões sobrepostas? O RBAC é um modelo aditivo, por isso as suas permissões eficazes são a soma das suas atribuições. Considere o seguinte exemplo em que um utilizador recebe a função de Contribuinte no âmbito da subscrição e o papel do Leitor num grupo de recursos. A soma das permissões do Contribuinte e das permissões do Leitor é efetivamente o papel de Contribuinte para o grupo de recursos. Portanto, neste caso, a atribuição do papel do Leitor não tem impacto.

![Atribuições de múltiplos papéis](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Atribuições de negação

Anteriormente, o RBAC era um modelo apenas de permissão sem negação, mas agora suporta atribuições de negação de forma limitada. Semelhante a uma atribuição de funções, uma *atribuição de negação* atribui um conjunto de ações de negação a um utilizador, grupo, diretor de serviço ou identidade gerida num âmbito específico para negar o acesso. Uma atribuição de papéis define um conjunto de ações que são *permitidas,* enquanto uma atribuição de negação define um conjunto de ações que não são *permitidas*. Por outras palavras, as atribuições de negação impedem os utilizadores de executarem ações especificadas, mesmo que uma atribuição de função lhes conceda acesso. As atribuições de negação têm precedência sobre as atribuições de funções. Para mais informações, consulte [Compreender negar atribuições para os recursos do Azure.](deny-assignments.md)

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>De que forma o RBAC determina se um utilizador tem acesso a um recurso

Seguem-se as etapas de alto nível que o RBAC utiliza para determinar se tem acesso a um recurso no plano de gestão. Isto pode ser útil para compreender se estiver a tentar resolver um problema de acesso.

1. Um utilizador (ou principal de serviço) obtém um token para o Azure Resource Manager.

    O token inclui as associações a grupos do utilizador (incluindo as associações de grupo transitivas).

1. O utilizador faz uma chamada da API de REST para o Azure Resource Manager com o token anexado.

1. O Azure Resource Manager obtém todas as atribuições de função e de negação que se aplicam ao recurso no qual a ação é executada.

1. O Azure Resource Manager restringe as atribuições de funções que se aplicam a este utilizador ou ao respetivo grupo e determina que funções o utilizador tem para este recurso.

1. O Azure Resource Manager determina se a ação na chamada de API está incluída nas funções que utilizador tem para este recurso.

1. Se o utilizador não tiver um papel com a ação no âmbito solicitado, o acesso não é concedido. Caso contrário, o Azure Resource Manager verifica se uma atribuição de negação se aplica.

1. Se uma atribuição de negação se aplicar, o acesso é bloqueado. Caso contrário, o acesso é concedido.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Veja o acesso que um utilizador tem aos recursos do Azure utilizando o portal Azure](check-access.md)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Compreender as diferentes funções no Azure](rbac-and-directory-admin-roles.md)
- [Adoção do Enterprise Cloud: gestão de acesso a recursos no Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
