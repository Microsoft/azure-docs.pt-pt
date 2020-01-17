---
title: Locatários, funções e usuários em cenários de Lighthouse do Azure
description: Entenda os conceitos de locatários Azure Active Directory, usuários e funções, além de como eles podem ser usados em cenários de Lighthouse do Azure.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 344e104201a83b3589dae6dbd3b02e49e4575e00
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156340"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Locatários, funções e usuários em cenários de Lighthouse do Azure

Antes de integrar os clientes para o [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md), é importante entender como os locatários, os usuários e as funções do Azure Active Directory (Azure AD) funcionam, além de como eles podem ser usados em cenários de Lighthouse do Azure.

Um *locatário* é uma instância dedicada e confiável do Azure AD. Normalmente, cada locatário representa uma única organização. O gerenciamento de recursos delegado do Azure permite a projeção lógica de recursos de um locatário para outro locatário. Isso permite que os usuários no locatário de gerenciamento (como um que pertença a um provedor de serviços) acessem recursos delegados no locatário de um cliente ou permite que [empresas com vários locatários Centralize suas operações de gerenciamento](enterprise.md).

Para alcançar essa projeção lógica, uma assinatura (ou um ou mais grupos de recursos em uma assinatura) no locatário do cliente deve ser *integrada* para o gerenciamento de recursos delegado do Azure. Esse processo de integração pode ser feito [por meio de modelos de Azure Resource Manager](../how-to/onboard-customer.md) ou [publicando uma oferta pública ou privada no Azure Marketplace](../how-to/publish-managed-services-offers.md).

Seja qual for o método de integração que você escolher, será necessário definir *autorizações*. Cada autorização especifica uma conta de usuário no locatário de gerenciamento que terá acesso aos recursos delegados e uma função interna que define as permissões que cada um desses usuários terá para esses recursos.

## <a name="role-support-for-azure-delegated-resource-management"></a>Suporte de função para o gerenciamento de recursos delegado do Azure

Ao definir uma autorização, cada conta de usuário deve ser atribuída a uma das [funções internas de RBAC (controle de acesso baseado em função)](../../role-based-access-control/built-in-roles.md). Não há suporte para funções personalizadas e [funções de administrador de assinatura clássica](../../role-based-access-control/classic-administrators.md) .

Atualmente, todas as [funções internas](../../role-based-access-control/built-in-roles.md) têm suporte com o gerenciamento de recursos delegado do Azure, com as seguintes exceções:

- Não há suporte para a função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) .
- Não há suporte para qualquer função interna com permissão de [Dataactions](../../role-based-access-control/role-definitions.md#dataactions) .
- Há suporte para a função interna de [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) , mas apenas para a finalidade limitada de atribuição de [funções a uma identidade gerenciada no locatário do cliente](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Nenhuma outra permissão geralmente concedida por essa função será aplicada. Se você definir um usuário com essa função, também deverá especificar as funções internas que esse usuário pode atribuir a identidades gerenciadas.

> [!NOTE]
> Quando uma nova função interna aplicável for adicionada ao Azure, ela poderá ser atribuída durante [a integração de um cliente usando modelos de Azure Resource Manager](../how-to/onboard-customer.md). Pode haver um atraso antes que a função recém-adicionada fique disponível no Portal do Cloud Partner ao [publicar uma oferta de serviço gerenciado](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Práticas recomendadas para definir usuários e funções

Ao criar suas autorizações, recomendamos as seguintes práticas recomendadas:

- Na maioria dos casos, você desejará atribuir permissões a um grupo de usuários ou entidade de serviço do Azure AD, em vez de uma série de contas de usuário individuais. Isso permite que você adicione ou remova o acesso para usuários individuais sem precisar atualizar e publicar o plano novamente quando seus requisitos de acesso forem alterados.
- Certifique-se de seguir o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho, ajudando a reduzir a chance de erros inadvertidos. Para obter mais informações, consulte [práticas recomendadas de segurança](../concepts/recommended-security-practices.md).
- Inclua um usuário com a [função de exclusão da atribuição de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para que você possa [remover o acesso à delegação](../how-to/onboard-customer.md#remove-access-to-a-delegation) posteriormente, se necessário. Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.
- Certifique-se de que qualquer usuário que precise [exibir a página meus clientes no portal do Azure](../how-to/view-manage-customers.md) tenha a função [leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclua acesso de leitor).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [práticas recomendadas de segurança para o gerenciamento de recursos delegado do Azure](recommended-security-practices.md).
- Integre seus clientes ao gerenciamento de recursos delegado do Azure, seja [usando modelos de Azure Resource Manager](../how-to/onboard-customer.md) ou [publicando uma oferta privada ou pública de serviços gerenciados para o Azure Marketplace](../how-to/publish-managed-services-offers.md).
