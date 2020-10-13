---
title: Passos para adicionar uma atribuição de papel - Azure RBAC
description: Aprenda os passos para atribuir funções Azure a utilizadores, grupos, princípios de serviço ou identidades geridas usando o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: badf10da8af0ed3829deb2498b51b5d5c8ce6a93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604412"
---
# <a name="steps-to-add-a-role-assignment"></a>Passos para adicionar uma atribuição de função

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve os passos de alto nível para adicionar uma atribuição de papel utilizando o [portal Azure](role-assignments-portal.md) [, Azure PowerShell,](role-assignments-powershell.md) [Azure CLI,](role-assignments-cli.md)ou a [API REST](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Passo 1: Determinar quem precisa de acesso

Primeiro tens de determinar quem precisa de acesso. Pode atribuir uma função a um utilizador, grupo, principal de serviço ou identidade gerida. Isto também é chamado de diretor de *segurança.*

![Principal de segurança para uma atribuição de função](./media/shared/rbac-security-principal.png)

- Utilizador – Um indivíduo que tem um perfil no Azure Active Directory. Também pode atribuir funções a utilizadores noutros inquilinos. Para obter informações sobre utilizadores noutras organizações, veja [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grupo – Um conjunto de utilizadores criado no Azure Active Directory. Quando atribui uma função a um grupo, todos os utilizadores nesse grupo têm essa função. 
- Principal de serviço – Uma identidade de segurança utilizada por aplicações ou serviços para aceder a recursos específicos do Azure. Pode considerá-lo como uma *identidade de utilizador* (nome de utilizador e palavra-passe ou certificado) para uma aplicação.
- Identidade gerida - Uma identidade no Azure Ative Directory que é gerida automaticamente pela Azure. Normalmente, utiliza [identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) ao desenvolver aplicações em nuvem para gerir as credenciais para autenticação nos serviços Azure.

## <a name="step-2-find-the-appropriate-role"></a>Passo 2: Encontrar o papel adequado

As permissões são agrupadas numa *definição de papel.* Normalmente chama-se *um papel.* Pode selecionar a partir de uma lista de várias funções incorporadas. Se as funções incorporadas não suprirem as necessidades específicas da sua organização, pode criar as suas próprias funções personalizadas.

![Definição de função para atribuição de função](./media/shared/rbac-role-definition.png)

São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

- [Proprietário](built-in-roles.md#owner) – tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outras pessoas.
- [Contribuidor](built-in-roles.md#contributor) – pode criar e gerir todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.
- [Leitor](built-in-roles.md#reader) - Pode ver os recursos Azure existentes.
- [Administrador de Acesso dos Utilizadores](built-in-roles.md#user-access-administrator) – permite gerir o acesso dos utilizadores aos recursos do Azure.

As restantes funções incorporadas permitem a gestão de recursos específicos do Azure. Por exemplo, a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) permite a um utilizador criar e gerir máquinas virtuais.

1. Comece com o artigo abrangente, [Azure incorporado.](built-in-roles.md) A tabela no topo do artigo é um índice para os detalhes mais tarde no artigo.

1. Nesse artigo, navegue na categoria de serviço (como computação, armazenamento e bases de dados) para obter o recurso a que pretende conceder permissões. A maneira mais fácil de encontrar o que procura é normalmente procurar na página uma palavra-chave relevante, como "blob", "máquina virtual", e assim por diante.

1. Reveja as funções listadas para a categoria de serviço e identifique as operações específicas de que necessita. Mais uma vez, comece sempre com o papel mais restritivo.

    Por exemplo, se um responsável de segurança precisar de ler bolhas numa conta de armazenamento Azure, mas não precisar de acesso por escrito, então escolha [o Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) em vez de Storage [Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) (e definitivamente não a função de proprietário de [dados](built-in-roles.md#storage-blob-data-owner) de armazenamento ao nível do administrador). Pode sempre atualizar as atribuições de funções mais tarde, se necessário.

1. Se não encontrar um papel adequado, pode criar um [papel personalizado.](custom-roles.md)

## <a name="step-3-identify-the-needed-scope"></a>Passo 3: Identificar o âmbito necessário

*Âmbito* é o conjunto de recursos a que o acesso se aplica. Em Azure, pode especificar um âmbito a quatro níveis: [grupo de gestão,](../governance/management-groups/overview.md)subscrição, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups)e recursos. Os âmbitos são estruturados numa relação de principal-subordinado. Cada nível de hierarquia torna o âmbito mais específico. Pode atribuir funções em qualquer um destes níveis de âmbito. O nível selecionado determina a largura da função. Níveis mais baixos herdam permissões de função de níveis mais altos. 

![Âmbito de uma atribuição de função](./media/shared/rbac-scope.png)

Quando atribui um papel no âmbito dos pais, essas permissões são herdadas para as miras da criança. Por exemplo:

- Se atribuir a função [Reader](built-in-roles.md#reader) a um utilizador no âmbito do grupo de gestão, esse utilizador pode ler tudo em todas as subscrições do grupo de gestão.
- Se atribuir o papel de [Billing Reader](built-in-roles.md#billing-reader) a um grupo no âmbito de subscrição, os membros desse grupo podem ler dados de faturação para cada grupo de recursos e recursos na subscrição.
- Se atribuir a função [Contribuidor](built-in-roles.md#contributor) a uma aplicação no âmbito do grupo de recursos, pode gerir recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na subscrição.

 Para mais informações, consulte [o âmbito de aplicação.](scope-overview.md)

## <a name="step-4-check-your-prerequisites"></a>Passo 4: Verifique os seus pré-requisitos

Para atribuir funções, tem de ser contratado com um utilizador que lhe seja atribuído uma função que tenha atribuições de funções a escrever permissão, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso](built-in-roles.md#user-access-administrator) ao Utilizador no âmbito em que está a tentar atribuir a função. Da mesma forma, para remover uma atribuição de funções, você deve ter as atribuições de funções eliminar permissão.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Se a sua conta de utilizador não tiver permissão para atribuir uma função dentro da sua subscrição, vê uma mensagem de erro que a sua conta "não tem autorização para executar ações 'Microsoft.Autorização/funAsignments/write'". Neste caso, contacte os administradores da sua subscrição, uma vez que podem atribuir as permissões em seu nome.

## <a name="step-5-add-role-assignment"></a>Passo 5. Adicionar atribuição de função

Assim que souberes o diretor de segurança, o papel e o alcance, podes atribuir o papel. Pode adicionar atribuições de funções utilizando o portal Azure PowerShell, Azure CLI, Azure SDKs ou REST APIs. Pode ter até **2000** atribuições de funções em cada subscrição. Este limite inclui atribuições de funções nos âmbitos de subscrição, grupo de recursos e recursos. Você pode ter até **500** atribuições de papel em cada grupo de gestão.

Confira os seguintes artigos para obter etapas detalhadas sobre como adicionar atribuições de funções.

- [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](role-assignments-portal.md)
- [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](role-assignments-powershell.md)
- [Utilizar a CLI do Azure para adicionar ou remover atribuições de funções do Azure](role-assignments-cli.md)
- [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](role-assignments-rest.md)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Conceder acesso a um utilizador aos recursos do Azure através do portal Azure](quickstart-assign-role-user-portal.md)
