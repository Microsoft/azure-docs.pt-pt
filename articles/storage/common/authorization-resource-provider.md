---
title: Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão
description: O fornecedor de recursos Azure Storage é um serviço que fornece acesso a recursos de gestão para o Armazenamento Azure. Pode utilizar o fornecedor de recursos de armazenamento Azure para criar, atualizar, gerir e eliminar recursos como contas de armazenamento, pontos finais privados e chaves de acesso à conta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789680"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão

O Azure Resource Manager é o serviço de implementação e gestão do Azure. O fornecedor de recursos de armazenamento Azure é um serviço baseado no Azure Resource Manager e que fornece acesso a recursos de gestão para armazenamento Azure. Pode utilizar o fornecedor de recursos de armazenamento Azure para criar, atualizar, gerir e eliminar recursos como contas de armazenamento, pontos finais privados e chaves de acesso à conta. Para obter mais informações sobre o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Pode utilizar o fornecedor de recursos Azure Storage para executar ações como criar ou eliminar uma conta de armazenamento ou obter uma lista de contas de armazenamento numa subscrição. Para autorizar pedidos contra o fornecedor de recursos de armazenamento Azure, utilize o Azure Ative Directory (Azure AD). Este artigo descreve como atribuir permissões a recursos de gestão, e aponta exemplos que mostram como fazer pedidos contra o fornecedor de recursos de armazenamento Azure.

## <a name="management-resources-versus-data-resources"></a>Recursos de gestão versus recursos de dados

A Microsoft fornece duas APIs REST para trabalhar com recursos de armazenamento Azure. Estas APIs formam a base de todas as ações que pode realizar contra o Azure Storage. A Azure Storage REST API permite-lhe trabalhar com dados na sua conta de armazenamento, incluindo bolhas, filas, ficheiros e dados de tabela. O fornecedor de recursos de armazenamento Azure REST API permite-lhe trabalhar com a conta de armazenamento e recursos conexos.

Um pedido que lê ou escreve dados blob requer permissões diferentes de um pedido que realiza uma operação de gestão. O Azure RBAC fornece um controlo fino sobre permissões a ambos os tipos de recursos. Quando atribuir um papel de Azure a um diretor de segurança, certifique-se de que entende quais as permissões que o diretor será concedido. Para uma referência detalhada que descreve quais as ações associadas a cada papel incorporado do Azure, consulte [as funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

O Azure Storage suporta a utilização do Azure AD para autorizar pedidos contra o armazenamento de Blob e Queue. Para obter informações sobre as funções Azure para operações de dados de bolhas e filas, consulte [o Acesso autorizado a bolhas e filas utilizando o Ative Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Atribuir permissões de gestão com controlo de acesso baseado em funções Azure (Azure RBAC)

Cada subscrição da Azure tem um Diretório Azure Ative associado que gere utilizadores, grupos e aplicações. Um utilizador, grupo ou aplicação também é referido como um principal de segurança no contexto da plataforma de [identidade](../../active-directory/develop/index.yml)microsoft . Pode conceder acesso a recursos numa subscrição a um diretor de segurança definido no Ative Directory utilizando o controlo de acesso baseado em funções da Azure (Azure RBAC).

Quando atribui um papel de Azure a um diretor de segurança, também indica o âmbito em que as permissões concedidas pelo papel estão em vigor. Para operações de gestão, pode atribuir uma função ao nível da subscrição, do grupo de recursos ou da conta de armazenamento. Pode atribuir uma função Azure a um principal de segurança utilizando o [portal Azure,](https://portal.azure.com/)as [ferramentas Azure CLI,](/cli/azure/install-classic-cli) [PowerShell](/powershell/azure/)ou o [fornecedor de recursos de armazenamento Azure REST API](/rest/api/storagerp).

Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md) e [funções de administrador de subscrição clássica, funções de Azure e funções de administrador AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Funções incorporadas para operações de gestão

A Azure fornece funções incorporadas que concedem permissões para chamadas de operações de gestão. O Azure Storage também fornece funções incorporadas especificamente para uso com o fornecedor de recursos de armazenamento Azure.

As funções incorporadas que concedem permissões para chamadas de operações de gestão de armazenamento incluem as funções descritas no quadro seguinte:

|    Função do Azure    |    Description    |    Inclui acesso às chaves da conta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietário** | Pode gerir todos os recursos de armazenamento e acesso a recursos.  | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Contribuinte**  | Pode gerir todos os recursos de armazenamento, mas não consegue gerir o acesso aos recursos. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Leitor** | Pode ver informações sobre a conta de armazenamento, mas não pode ver as chaves da conta. | N.º |
| **Contribuidor de Conta de Armazenamento** | Pode gerir a conta de armazenamento, obter informações sobre os grupos e recursos de recursos da subscrição, e criar e gerir implementações de grupos de recursos de subscrição. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Administrador de Acesso do Utilizador** | Pode gerir o acesso à conta de armazenamento.   | Sim, permite que um diretor de segurança atribua quaisquer permissões a si mesmos e a outros. |
| **Contribuidor de Máquina Virtual** | Pode gerir máquinas virtuais, mas não a conta de armazenamento à qual estão ligadas.   | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |

A terceira coluna da tabela indica se a função incorporada suporta o **Microsoft.Storage/storageAccounts/listkeys/ação**. Esta ação permite a leitura e regeneração das chaves da conta de armazenamento. As permissões de acesso aos recursos de gestão do Azure Storage também não incluem permissões de acesso aos dados. No entanto, se um utilizador tiver acesso às chaves da conta, então pode utilizar as chaves da conta para aceder aos dados do Azure Storage através da autorização da Chave Partilhada.

### <a name="custom-roles-for-management-operations"></a>Funções personalizadas para operações de gestão

O Azure também apoia a definição de funções personalizadas da Azure para o acesso aos recursos de gestão. Para obter mais informações sobre funções personalizadas, consulte [as funções personalizadas Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Exemplos de código

Para exemplos de código que mostram como autorizar e chamar operações de gestão das bibliotecas de gestão de armazenamento Azure, consulte as seguintes amostras:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Gestor de Recursos Azure contra implementações clássicas

Estes modelos representam duas formas distingas de implementar e gerir as suas soluções do Azure. A Microsoft recomenda a utilização do modelo de implementação do Gestor de Recursos Azure quando criar uma nova conta de armazenamento. Se possível, a Microsoft também recomenda que recrie as contas de armazenamento clássicas existentes com o modelo Resource Manager. Embora possa criar uma conta de armazenamento utilizando o modelo de implementação clássico, o modelo clássico é menos flexível e acabará por ser depreciado.

Para obter mais informações sobre os modelos de implementação da Azure, consulte [o Gestor de Recursos e a implementação clássica.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
- [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Metas de escalabilidade para o fornecedor de recursos de armazenamento Azure](scalability-targets-resource-provider.md)