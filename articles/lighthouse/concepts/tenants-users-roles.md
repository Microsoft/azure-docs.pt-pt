---
title: Inquilinos, utilizadores e papéis em cenários do Farol de Azure
description: Compreenda os conceitos de inquilinos, utilizadores e funções do Azure Ative Directory, bem como como podem ser usados em cenários do Farol Azure.
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 411b9bae19166e1875011360aa011c05d590b237
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043044"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Inquilinos, utilizadores e papéis em cenários do Farol de Azure

Antes de embarcar clientes para [o Azure Lighthouse,](../overview.md)é importante entender como os inquilinos, utilizadores e funções da Azure Ative Directory (Azure AD) funcionam, bem como como podem ser usados em cenários do Farol Azure.

Um *inquilino* é um caso dedicado e de confiança da Azure AD. Normalmente, cada inquilino representa uma única organização. [A gestão de recursos delegados Azure](azure-delegated-resource-management.md) permite a projeção lógica de recursos de um inquilino para outro inquilino. Isto permite que os utilizadores do inquilino gerente (como um pertencente a um prestador de serviços) acedam a recursos delegados no inquilino de um cliente, ou permitem que [empresas com vários inquilinos centralizem as suas operações de gestão.](enterprise.md)

Para conseguir esta projeção lógica, uma subscrição (ou um ou mais grupos de recursos dentro de uma subscrição) no arrendatário do cliente deve ser *a bordo* do Farol de Azure. Este processo de embarque pode ser feito [através de modelos Azure Resource Manager](../how-to/onboard-customer.md) ou através da publicação de uma oferta pública ou privada ao [Azure Marketplace.](../how-to/publish-managed-services-offers.md)

Seja qual for o método de embarque que escolher, terá de definir *autorizações.* Cada autorização especifica uma conta de utilizador no inquilino gerente que terá acesso aos recursos delegados, e uma função incorporada que define as permissões que cada um destes utilizadores terá para estes recursos.

## <a name="best-practices-for-defining-users-and-roles"></a>Melhores práticas para definir utilizadores e funções

Ao criar as suas autorizações, recomendamos as seguintes boas práticas:

- Na maioria dos casos, irá querer atribuir permissões a um grupo de utilizadores ou ao serviço principal da Azure, em vez de uma série de contas individuais de utilizador. Isto permite-lhe adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e reeditar o plano quando os seus requisitos de acesso mudarem.
- Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho, ajudando a reduzir a chance de erros inadvertidos. Para mais informações, consulte [as práticas de segurança recomendadas.](../concepts/recommended-security-practices.md)
- Inclua um utilizador com a [Função de Registo de Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para que possa [remover o acesso à delegação](../how-to/remove-delegation.md) mais tarde, se necessário. Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no arrendatário do cliente.
- Certifique-se de que qualquer utilizador que precise de ver a [página dos meus clientes no portal Azure](../how-to/view-manage-customers.md) tem a função [Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui o acesso ao Reader).

> [!IMPORTANT]
> Para adicionar permissões para um grupo AD Azure, o **tipo de grupo** deve ser definido como **Segurança** . Esta opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="role-support-for-azure-lighthouse"></a>Apoio de função para o Farol de Azure

Ao definir uma autorização, cada conta de utilizador deve ser atribuída a uma das [funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md). As funções personalizadas e [as funções clássicas de administrador de subscrição](../../role-based-access-control/classic-administrators.md) não são suportadas.

Todas as [funções incorporadas](../../role-based-access-control/built-in-roles.md) são atualmente suportadas com o Farol Azure, com as seguintes exceções:

- A função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) não é suportada.
- Quaisquer funções incorporadas com permissão [DataActions](../../role-based-access-control/role-definitions.md#dataactions) não são suportadas.
- A função [incorporada do Administrador de Acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador é suportada, mas apenas com o final limitado de atribuir [funções a uma identidade gerida no arrendatário do cliente.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) Não serão aplicadas outras permissões normalmente concedidas por esta função. Se definir um utilizador com esta função, também deve especificar as funções incorporadas que este utilizador pode atribuir às identidades geridas.

> [!NOTE]
> Uma vez que uma nova função incorporada aplicável é adicionada ao Azure, pode ser atribuída ao [embarcar um cliente usando modelos de Gestor de Recursos Azure](../how-to/onboard-customer.md). Pode haver um atraso antes de o papel recém-adicionado ficar disponível no Partner Center ao [publicar uma oferta de serviço gerida](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais [sobre as práticas de segurança recomendadas para o Farol Azure.](recommended-security-practices.md)
- A bordo dos seus clientes para o Azure Lighthouse, quer [utilizando modelos de Gestor de Recursos Azure,](../how-to/onboard-customer.md) quer [publicando uma oferta de serviços geridos por empresas privadas ou públicas ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)
