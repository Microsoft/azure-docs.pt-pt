---
title: Escolha como autorizar o acesso aos dados da fila no portal Azure
titleSuffix: Azure Storage
description: Ao aceder aos dados da fila utilizando o portal Azure, o portal faz pedidos para o Azure Storage sob as capas. Estes pedidos para o Azure Storage podem ser autenticados e autorizados usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 504d2eb939758e6045a2af095c66093c8754cb94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590754"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Escolha como autorizar o acesso aos dados da fila no portal Azure

Quando acede aos dados da fila utilizando o [portal Azure,](https://portal.azure.com)o portal faz pedidos para o Azure Storage sob as capas. Um pedido para a Azure Storage pode ser autorizado usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento. O portal indica qual o método que está a utilizar e permite-lhe alternar entre os dois se tiver as permissões adequadas.

## <a name="permissions-needed-to-access-queue-data"></a>Permissões necessárias para aceder aos dados da fila

Dependendo da forma como pretende autorizar o acesso aos dados da fila no portal Azure, necessitará de permissões específicas. Na maioria dos casos, estas permissões são fornecidas através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre o Azure RBAC, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Use a chave de acesso à conta

Para aceder aos dados da fila com a chave de acesso à conta, tem de ter uma função Azure que inclui a ação do Azure `Microsoft.Storage/storageAccounts/listkeys/action` RBAC. Este papel de Azure pode ser um papel incorporado ou personalizado. As funções incorporadas que `Microsoft.Storage/storageAccounts/listkeys/action` suportam incluem:

- A [função de proprietário](../../role-based-access-control/built-in-roles.md#owner) de recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) do Gestor de Recursos Azure
- A [função de contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando tenta aceder aos dados da fila no portal Azure, o portal verifica primeiro se lhe foi atribuído um papel com `Microsoft.Storage/storageAccounts/listkeys/action` . Se lhe foi atribuído um papel com esta ação, então o portal utiliza a chave de conta para aceder aos dados da fila. Se não lhe foi atribuído um papel com esta ação, então o portal tenta aceder aos dados utilizando a sua conta AZure AD.

> [!NOTE]
> As funções clássicas de administrador de subscrição **Administrador** e **Coadministrador** incluem o equivalente à função de Gestor de Recursos [`Owner`](../../role-based-access-control/built-in-roles.md#owner) Azure. A função **Proprietário** inclui todas as ações, incluindo a `Microsoft.Storage/storageAccounts/listkeys/action` , para que um utilizador com uma destas funções administrativas também possa aceder aos dados da fila com a chave da conta. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Use a sua conta AZure AD

Para aceder aos dados da fila a partir do portal Azure utilizando a sua conta Azure AD, ambas as seguintes declarações devem ser verdadeiras para si:

- Foi-lhe atribuída a função de Gestor de Recursos Azure, [`Reader`](../../role-based-access-control/built-in-roles.md#reader) no mínimo, ao nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gestor de Recursos Azure que concede acesso aos recursos de gestão de conta de armazenamento também é aceitável.
- Foi-lhe atribuído um papel incorporado ou personalizado que dá acesso aos dados da fila.

A atribuição de funções **de Reader** ou outra atribuição de função do Gestor de Recursos Azure é necessária para que o utilizador possa visualizar e navegar recursos de gestão de conta de armazenamento no portal Azure. As funções Azure que concedem acesso aos dados de fila não concedem acesso aos recursos de gestão de conta de armazenamento. Para aceder aos dados da fila no portal, o utilizador necessita de permissões para navegar nos recursos da conta de armazenamento. Para obter mais informações sobre este requisito, consulte [atribuir a função Reader para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções incorporadas que suportam o acesso aos dados da sua fila incluem:

- [Colaborador de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Leia/escreva/elimine permissões para filas.
- [Leitor de dados de fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Permissões apenas de leitura para filas.

As funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre a criação de funções personalizadas [Azure, consulte os papéis personalizados da Azure](../../role-based-access-control/custom-roles.md) e [compreenda definições de funções para recursos Azure.](../../role-based-access-control/role-definitions.md)

A listagem de filas com uma função clássica de administrador de subscrição não é suportada. Para listar as filas, um utilizador deve ter-lhes atribuído a função de **Leitor** de Gestor de Recursos Azure, a função **de Leitor de Dados de Fila de Armazenamento** ou a função de Contribuinte de **Dados de Fila de Armazenamento.**

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta a utilização de credenciais AZure AD para visualizar e modificar dados de fila. O Explorador de Armazenamento no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, deve ser-lhe atribuída uma função que inclua `Microsoft.Storage/storageAccounts/listkeys/action` .

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navegue para filas no portal Azure

Para ver os dados da fila no portal, navegue na **Visão Geral** para a sua conta de armazenamento e clique nos links para **filas.** Em alternativa, pode navegar para a secção **de serviço de Fila** no menu.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Screenshot mostrando como navegar para os dados da fila no portal Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Ao navegar para uma fila, o portal Azure indica se está a utilizar a chave de acesso à conta ou a sua conta AZure AD para autenticar.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso à conta

Se estiver a autenticar utilizando a chave de acesso à conta, verá a **Chave de Acesso** especificada como o método de autenticação no portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Screenshot mostrando o utilizador que atualmente acede às filas com a chave da conta":::

Para mudar para a conta Azure AD, clique no link realçado na imagem. Se tiver as permissões apropriadas através das funções Azure que lhe são atribuídas, poderá prosseguir. No entanto, se não tiver as permissões certas, verá uma mensagem de erro como a seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Erro mostrado se a conta AD Azure não suporta acesso":::

Note que não aparecem filas na lista se a sua conta AZure AD não tiver permissões para as visualizar. Clique no **Switch para aceder ao** link chave para utilizar novamente a chave de acesso para autenticação.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com a sua conta AZure AD

Se estiver a autenticar utilizando a sua conta Azure AD, verá **a Conta de Utilizador AZure AD** especificada como o método de autenticação no portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Screenshot mostrando o utilizador atualmente a aceder a filas com conta AZure AD":::

Para mudar para a utilização da chave de acesso à conta, clique no link realçado na imagem. Se tiver acesso à chave da conta, poderá prosseguir. No entanto, se não tiver acesso à tecla da conta, o portal Azure apresenta uma mensagem de erro.

As filas não estão listadas no portal se não tiver acesso às chaves da conta. Clique no link **'Conta de Utilizador AD' Azure** para utilizar novamente a sua conta Azure AD para autenticação.

## <a name="next-steps"></a>Passos seguintes

- [Autenticar acesso a bolhas e filas Azure usando o Azure Ative Directory](../common/storage-auth-aad.md)
- [Utilize o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-portal.md)
- [Utilize o CLI Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-cli.md)
- [Utilize o módulo Azure PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-powershell.md)
