---
title: Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão
description: O fornecedor de recursos de armazenamento Azure é um serviço que fornece acesso a recursos de gestão para armazenamento Azure. Pode utilizar o fornecedor de recursos de Armazenamento Azure para criar, atualizar, gerir e eliminar recursos como contas de armazenamento, pontos finais privados e chaves de acesso à conta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972353"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão

O Azure Resource Manager é o serviço de implementação e gestão do Azure. O fornecedor de recursos de armazenamento Azure é um serviço baseado no Azure Resource Manager e que fornece acesso a recursos de gestão para armazenamento Azure. Pode utilizar o fornecedor de recursos de Armazenamento Azure para criar, atualizar, gerir e eliminar recursos como contas de armazenamento, pontos finais privados e chaves de acesso à conta. Para mais informações sobre o Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](/azure/azure-resource-manager/resource-group-overview)

Pode utilizar o fornecedor de recursos de Armazenamento Azure para executar ações como criar ou apagar uma conta de armazenamento ou obter uma lista de contas de armazenamento numa subscrição. Para autorizar pedidos contra o fornecedor de recursos de armazenamento Azure, utilize o Azure Ative Directory (Azure AD). Este artigo descreve como atribuir permissões aos recursos de gestão, e aponta exemplos que mostram como fazer pedidos contra o fornecedor de recursos de armazenamento Azure.

## <a name="management-resources-versus-data-resources"></a>Recursos de gestão versus recursos de dados

A Microsoft fornece duas APIs REST para trabalhar com os recursos de Armazenamento Azure. Estas APIs formam a base de todas as ações que pode executar contra o Armazenamento Azure. A API do Rest de Armazenamento Azure permite-lhe trabalhar com dados na sua conta de armazenamento, incluindo blob, fila, ficheiro e dados de mesa. O fornecedor de recursos de armazenamento Azure REST API permite-lhe trabalhar com a conta de armazenamento e recursos relacionados.

Um pedido que lê ou escreve dados blob requer permissões diferentes do que um pedido que executa uma operação de gestão. O RBAC fornece um controlo fino sobre permissões a ambos os tipos de recursos. Quando atribuir um papel RBAC a um diretor de segurança, certifique-se de que compreende as permissões que esse diretor será concedido. Para uma referência detalhada que descreve quais as ações associadas a cada papel rBAC incorporado, consulte [funções incorporadas para os recursos Azure.](../../role-based-access-control/built-in-roles.md)

O Azure Storage suporta a utilização de AD Azure para autorizar pedidos contra o armazenamento de Blob e Fila. Para obter informações sobre as funções RBAC para operações de dados blob e fila, consulte [Autorizar o acesso a blobs e filas utilizando o Diretório Ativo](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Atribuir permissões de gestão com controlo de acesso baseado em funções (RBAC)

Cada subscrição do Azure tem um Diretório Ativo Azure associado que gere utilizadores, grupos e aplicações. Um utilizador, grupo ou aplicação também é referido como um principal de segurança no contexto da [plataforma de identidade microsoft](/azure/active-directory/develop/). Pode conceder acesso a recursos numa subscrição a um diretor de segurança definido no Diretório Ativo utilizando o controlo de acesso baseado em funções (RBAC).

Ao atribuir uma função RBAC a um diretor de segurança, também indica o âmbito a que estão em vigor as permissões concedidas pelo papel. Para operações de gestão, pode atribuir uma função ao nível da subscrição, do grupo de recursos ou da conta de armazenamento. Pode atribuir uma função RBAC a um diretor de segurança utilizando o [portal Azure,](https://portal.azure.com/)as [ferramentas Azure CLI,](../../cli-install-nodejs.md) [PowerShell](/powershell/azureps-cmdlets-docs)ou o fornecedor de recursos de [armazenamento Azure REST API](/rest/api/storagerp).

Para obter mais informações sobre o RBAC, consulte O que é [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](../../role-based-access-control/rbac-and-directory-admin-roles.md)o controlo de acesso baseado [em papéis (RBAC) para os recursos Azure?](../../role-based-access-control/overview.md)

### <a name="built-in-roles-for-management-operations"></a>Funções incorporadas para operações de gestão

A Azure fornece funções incorporadas que concedem permissões para operações de gestão de chamadas. O Azure Storage também fornece funções incorporadas especificamente para utilização com o fornecedor de recursos de armazenamento Azure.

As funções incorporadas que concedem permissões para operações de gestão de armazenamento incluem as funções descritas no quadro seguinte:

|    Função RBAC    |    Descrição    |    Inclui acesso às chaves da conta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietário** | Pode gerir todos os recursos de armazenamento e acesso aos recursos.  | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Contribuinte**  | Pode gerir todos os recursos de armazenamento, mas não consegue gerir a atribuição de recursos. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Leitor** | Pode ver informações sobre a conta de armazenamento, mas não pode ver as chaves da conta. | Não. |
| **Contribuidor de Conta de Armazenamento** | Pode gerir a conta de armazenamento, obter informações sobre os grupos e recursos da subscrição e criar e gerir implementações de grupos de recursos de subscrição. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Administrador de Acesso do Utilizador** | Pode gerir o acesso à conta de armazenamento.   | Sim, permite que um diretor de segurança atribua permissões a si e aos outros. |
| **Contribuidor de Máquina Virtual** | Pode gerir máquinas virtuais, mas não a conta de armazenamento à qual estão ligadas.   | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |

A terceira coluna da tabela indica se a função incorporada suporta a **Microsoft.Storage/storageAccounts/listkeys/action**. Esta ação concede permissões para ler e regenerar as chaves da conta de armazenamento. As permissões para aceder aos recursos de gestão do Armazenamento Azure também não incluem permissões para aceder a dados. No entanto, se um utilizador tiver acesso às chaves da conta, poderá utilizar as chaves da conta para aceder aos dados do Armazenamento Azure através da autorização da Chave Partilhada.

### <a name="custom-roles-for-management-operations"></a>Funções personalizadas para operações de gestão

O Azure também apoia a definição de funções rBAC personalizadas para o acesso aos recursos de gestão. Para obter mais informações sobre papéis personalizados, consulte [funções personalizadas para os recursos Do Azure.](../../role-based-access-control/custom-roles.md)

## <a name="code-samples"></a>Exemplos de código

Para exemplos de código que mostrem como autorizar e chamar operações de gestão de chamadas a partir das bibliotecas de gestão de armazenamento azure, consulte as seguintes amostras:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Nó.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Pitão](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Gestor de Recursos Azure versus implantações clássicas

Estes modelos representam duas formas distingas de implementar e gerir as suas soluções do Azure. A Microsoft recomenda a utilização do modelo de implementação do Gestor de Recursos Azure quando criar uma nova conta de armazenamento. Se possível, a Microsoft também recomenda que recrie as contas de armazenamento clássicas existentes com o modelo DeS Manager. Embora possa criar uma conta de armazenamento utilizando o modelo de implementação clássico, o modelo clássico é menos flexível e acabará por ser depreciado.

Para obter mais informações sobre os modelos de implantação do Azure, consulte [O Gestor de Recursos e a implantação clássica.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Gestor de Recursos Azure](/azure/azure-resource-manager/resource-group-overview)
- [O que é o controlo de acesso baseado em funções (RBAC) dos recursos do Azure?](../../role-based-access-control/overview.md)
- [Metas de escalabilidade para o fornecedor de recursos de armazenamento Azure](scalability-targets-resource-provider.md)
