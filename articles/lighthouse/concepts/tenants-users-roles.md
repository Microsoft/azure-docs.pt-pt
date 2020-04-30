---
title: Inquilinos, funções e utilizadores em cenários do Farol Azure
description: Compreenda os conceitos de inquilinos, utilizadores e funções do Azure Ative Directory, bem como como podem ser utilizados em cenários do Farol Azure.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7ed5af18efbb0f5b97dcab20093cc45e8bed1d03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144924"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Inquilinos, funções e utilizadores em cenários do Farol Azure

Antes de embarcar em clientes para [a gestão de recursos delegados do Azure,](azure-delegated-resource-management.md)é importante entender como funcionam os inquilinos, utilizadores e funções do Azure Ative Directory (Azure AD), bem como como podem ser utilizados em cenários do Farol Azure.

Um *inquilino* é um caso dedicado e de confiança da Azure AD. Normalmente, cada inquilino representa uma única organização. A gestão de recursos delegados da Azure permite a projeção lógica de recursos de um inquilino para outro inquilino. Isto permite que os utilizadores do inquilino gerente (como um pertencente a um prestador de serviços) acedam aos recursos delegados no inquilino de um cliente, ou permitem que [empresas com vários inquilinos centralizem as suas operações de gestão.](enterprise.md)

Para alcançar esta projeção lógica, uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição) no inquilino do cliente deve ser *a bordo* para a gestão de recursos delegados do Azure. Este processo de embarque pode ser feito através de modelos do Gestor de [Recursos Do Azure](../how-to/onboard-customer.md) ou através da publicação de [uma oferta pública ou privada ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)

Seja qual for o método de embarque que escolher, terá de definir *autorizações.* Cada autorização especifica uma conta de utilizador no inquilino gestor que terá acesso aos recursos delegados, e uma função incorporada que define as permissões que cada um destes utilizadores terá para estes recursos.

## <a name="role-support-for-azure-delegated-resource-management"></a>Apoio ao papel para a gestão de recursos delegados do Azure

Ao definir uma autorização, cada conta de utilizador deve ser atribuída a uma das [funções incorporadas de controlo de acesso baseados em funções (RBAC).](../../role-based-access-control/built-in-roles.md) As funções personalizadas e as [funções clássicas](../../role-based-access-control/classic-administrators.md) de administrador de subscrição não são suportadas.

Todas as [funções incorporadas](../../role-based-access-control/built-in-roles.md) são atualmente apoiadas com a gestão de recursos delegados do Azure, com as seguintes exceções:

- O papel [de Proprietário](../../role-based-access-control/built-in-roles.md#owner) não é apoiado.
- Quaisquer funções incorporadas com permissão [DataActions](../../role-based-access-control/role-definitions.md#dataactions) não são suportadas.
- O papel integrado do Administrador de Acesso ao [Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) é suportado, mas apenas com o propósito limitado de [atribuir funções a uma identidade gerida no inquilino do cliente.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) Não serão aplicadas outras permissões normalmente concedidas por este papel. Se definir um utilizador com esta função, deve também especificar as funções incorporadas que este utilizador pode atribuir a identidades geridas.

> [!NOTE]
> Uma vez que uma nova função incorporada aplicável é adicionada ao Azure, pode ser atribuída ao [embarcar num cliente usando modelos](../how-to/onboard-customer.md)de Gestor de Recursos Azure . Pode haver um atraso antes que a função recém-adicionada fique disponível no Cloud Partner Portal ao publicar uma oferta de [serviço gerida.](../how-to/publish-managed-services-offers.md)

## <a name="best-practices-for-defining-users-and-roles"></a>Boas práticas para definir utilizadores e funções

Ao criar as suas autorizações, recomendamos as seguintes melhores práticas:

- Na maioria dos casos, pretende atribuir permissões a um grupo de utilizadores ou prestador de serviços da Azure AD, em vez de uma série de contas individuais de utilizadores. Isto permite adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e republicar o plano quando os seus requisitos de acesso mudarem.
- Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho, ajudando a reduzir a possibilidade de erros inadvertidos. Para mais informações, consulte [práticas de segurança recomendadas.](../concepts/recommended-security-practices.md)
- Inclua um utilizador com a Função de Eliminação de [Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para que possa [remover o acesso à delegação](../how-to/remove-delegation.md) mais tarde, se necessário. Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no inquilino do cliente.
- Certifique-se de que qualquer utilizador que precise [de ver a página dos meus clientes no portal Azure](../how-to/view-manage-customers.md) tem a função [Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui o acesso ao Leitor).

> [!IMPORTANT]
> Para adicionar permissões a um grupo Azure AD, o **tipo de grupo** deve ser **segurança** e não o **Office 365**. Esta opção é selecionada quando o grupo é criado. Para mais informações, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Conheça as [práticas de segurança recomendadas para a gestão de recursos delegados do Azure.](recommended-security-practices.md)
- A bordo dos seus clientes para a gestão de recursos delegados do Azure, quer utilizando modelos de [Gestor de Recursos Azure,](../how-to/onboard-customer.md) quer [publicando uma oferta de serviços geridos pelo público ou privado ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)
