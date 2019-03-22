---
title: Utilizar o portal do Azure para aceder a BLOBs ou filas de dados - armazenamento do Azure
description: Quando aceder a BLOBs ou os dados de fila com o portal do Azure, o portal faz solicitações ao armazenamento do Azure nos bastidores. Estes pedidos ao armazenamento do Azure podem ser autenticados e autorizados usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bc4896f634bb98b4663dfc3fbc6bad7ba9bace3e
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319705"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Utilizar o portal do Azure para aceder a dados de BLOBs ou filas

Quando acede a BLOBs ou filas de dados com o [portal do Azure](https://portal.azure.com), o portal faz solicitações ao armazenamento do Azure nos bastidores. Estes pedidos ao armazenamento do Azure podem ser autenticados e autorizados usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento. O portal indica qual método de autenticação estiver a utilizar e permite-lhe alternar entre os dois, se tiver as permissões adequadas.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para aceder aos dados de BLOBs ou filas

Dependendo de como deseja autenticar dados de BLOBs ou filas de acesso no portal do Azure, terá de permissões específicas. Na maioria dos casos, estas permissões são fornecidas através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre RBAC, veja [o que é o controlo de acesso baseado em funções (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Chave de acesso de conta

Para aceder aos dados de BLOBs e filas com a chave de acesso de conta, tem de ter uma função RBAC atribuída ao utilizador que inclui a ação de RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Esta função RBAC pode ser uma incorporadas ou uma função personalizada. Funções incorporadas que suportam **Microsoft.Storage/storageAccounts/listkeys/action** incluem:

- O Azure Resource Manager [proprietário](../../role-based-access-control/built-in-roles.md#owner) função
- O Azure Resource Manager [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) função
- O [contribuinte de conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) função

Quando tentar aceder aos dados de BLOBs ou filas no portal do Azure, o portal do verifica primeiro se lhe tenha sido atribuída uma função com **Microsoft.Storage/storageAccounts/listkeys/action**. Se lhe tenha sido atribuída uma função com esta ação, em seguida, o portal utiliza a chave da conta para aceder aos dados de BLOBs e filas. Se não tiver sido atribuído uma função com esta ação, em seguida, o portal tenta aceder a dados com a sua conta do Azure AD.

> [!NOTE]
> As funções de administrador de subscrição clássica, administrador de serviços e Coadministrador incluem o equivalente do Azure Resource Manager [proprietário](../../role-based-access-control/built-in-roles.md#owner) função. O **proprietário** função inclui todas as ações, incluindo o **Microsoft.Storage/storageAccounts/listkeys/action**, por isso, um utilizador com uma destas funções administrativas também pode aceder a dados de BLOBs e filas com o chave da conta. Para obter mais informações, consulte [funções de administrador de subscrição clássica](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Conta do Azure AD

Para aceder aos dados de BLOBs ou filas do portal do Azure com a sua conta do Azure AD, as duas das declarações a seguir devem ser verdadeiras para:

- Foi atribuído o Azure Resource Manager [leitor](../../role-based-access-control/built-in-roles.md#reader) função, no mínimo, âmbito para o nível da conta de armazenamento ou superior. O **leitor** função concede as permissões mais restritas, mas outra função do Azure Resource Manager que concede acesso a recursos de gerenciamento de conta de armazenamento também é aceitável.
- Foi atribuído a uma função de interna ou personalizada que fornece acesso a dados de fila ou de blobs.

O **leitor** atribuição de função ou outra atribuição de função do Azure Resource Manager é necessária para que o usuário pode exibir e navegar recursos de gerenciamento de conta de armazenamento no portal do Azure. As funções RBAC que concedem acesso aos dados de BLOBs ou filas não conceda acesso a recursos de gerenciamento de conta de armazenamento. Para aceder a dados de BLOBs ou filas no portal, o utilizador tem permissões para navegar de recursos da conta de armazenamento. Para obter mais informações sobre este requisito, consulte [atribuir a função de leitor para acesso ao portal](../common/storage-auth-aad-rbac.md#assign-the-reader-role-for-portal-access).

As funções incorporadas que suportam o acesso aos seus dados de BLOBs ou filas incluem:

- [Proprietário de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Para POSIX controlo de acesso para a geração 2 de armazenamento do Azure Data Lake (pré-visualização).
- [Contribuinte de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Permissões de leitura/escrita/eliminação para blobs.
- [Leitor de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Permissões só de leitura para blobs.
- [Contribuinte de dados de fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Permissões de leitura/escrita/eliminar para filas.
- [Leitor de dados de fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Permissões só de leitura para filas.
    
Funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre como criar funções RBAC personalizadas, consulte [funções personalizadas para recursos do Azure](../../role-based-access-control/custom-roles.md) e [compreender as definições de funções para recursos do Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Listagem filas com uma função de administrador de subscrição clássica não é suportada. Para listar filas, tem de ter atribuída um utilizador para o Azure Resource Managê-los **leitor** função, o **leitor de dados de fila de armazenamento** função, ou o **contribuinte de dados de fila de armazenamento** função.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegue para blobs ou filas no portal

Para ver os dados de BLOBs ou filas no portal, navegue para o **descrição geral** para a conta de armazenamento e clique nos links para **Blobs** ou **filas**. Em alternativa, pode navegar para o **serviço Blob** e **serviço fila** secções no menu. 

![Navegue para dados de BLOBs ou filas no portal do Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando navega para um contentor ou uma fila, o portal do Azure indica se estiver a utilizar a chave de acesso de conta ou a sua conta do Azure AD para autenticar.

Os exemplos nesta secção mostram a aceder a um contentor e respetivos blobs, mas o portal apresenta a mesma mensagem durante a aceder a uma fila e suas mensagens, ou filas de listagem.

### <a name="account-access-key"></a>Chave de acesso de conta

Se está a autenticar com a chave de acesso de conta, verá **chave de acesso** especificado como o método de autenticação no portal do:

![Atualmente a aceder aos dados de contentor com a chave de conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para mudar para utilizar a conta do Azure AD, clique na ligação realçada na imagem. Se tiver as permissões adequadas por meio das funções RBAC que estão atribuídas a, poderá continuar. No entanto, se não tem as permissões corretas, verá uma mensagem de erro semelhante ao seguinte:

![Erro mostrado se conta do Azure AD não suporta o acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Tenha em atenção que não blobs são apresentados na lista se a sua conta do Azure AD não tem permissões para visualizá-los. Clique nas **mudar para a chave de acesso** ligação para utilizar a chave de acesso para a autenticação novamente.

### <a name="azure-ad-account"></a>Conta do Azure AD

Se está a autenticar com a sua conta do Azure AD, verá **conta de utilizador do Azure AD** especificado como o método de autenticação no portal do:

![Acessando dados de contentor com a conta do Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para mudar para utilizar a chave de acesso de conta, clique na ligação realçada na imagem. Se tiver acesso à chave de conta, em seguida, poderá continuar. No entanto, se não tem acesso à chave de conta, verá uma mensagem de erro semelhante ao seguinte:

![Erro mostrado se não tiver acesso à chave de conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Tenha em atenção que não blobs são apresentados na lista se não tiver acesso às chaves de conta. Clique nas **mudar para a conta de utilizador do Azure AD** ligação para utilizar a sua conta do Azure AD para a autenticação novamente.

## <a name="next-steps"></a>Passos Seguintes

- [Autenticar o acesso a blobs do Azure e filas com o Azure Active Directory (pré-visualização)](storage-auth-aad.md)
- [Conceder acesso a contentores do Azure e filas com RBAC no portal do Azure (pré-visualização)](storage-auth-aad-rbac.md)