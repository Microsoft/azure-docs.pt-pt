---
title: Utilize o portal Azure para aceder a dados de blob ou fila
titleSuffix: Azure Storage
description: Ao aceder a dados de blob ou fila utilizando o portal Azure, o portal faz pedidos ao Armazenamento Azure sob as capas. Estes pedidos ao Armazenamento Azure podem ser autenticados e autorizados usando a sua conta AD Azure ou a chave de acesso à conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866898"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Utilize o portal Azure para aceder a dados de blob ou fila

Quando acede a dados de blob ou fila utilizando o [portal Azure,](https://portal.azure.com)o portal faz pedidos ao Armazenamento Azure sob as capas. Um pedido ao Armazenamento Azure pode ser autorizado usando a sua conta AD Azure ou a chave de acesso à conta de armazenamento. O portal indica qual o método que está a usar e permite alternar entre os dois se tiver as permissões adequadas.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para aceder a dados de blob ou fila

Dependendo de como pretende autorizar o acesso a dados blob ou de fila no portal Azure, necessitará de permissões específicas. Na maioria dos casos, estas permissões são fornecidas através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre o RBAC, consulte [O que é o controlo de acesso baseado em papéis (RBAC)?](../../role-based-access-control/overview.md)

### <a name="account-access-key"></a>Chave de acesso à conta

Para aceder aos dados blob e de fila com a chave de acesso à conta, deve ter uma função RBAC que inclua a ação RBAC **Microsoft.StorageAccounts/listkeys/action**. Este papel RBAC pode ser um papel incorporado ou personalizado. As funções incorporadas que suportam a **Microsoft.Storage/storageAccounts/listkeys/action** incluem:

- O papel de [Proprietário do Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Recursos Azure
- A função de [Contribuinte da Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando tenta aceder a dados blob ou de fila no portal Azure, o portal verifica primeiro se lhe foi atribuída uma função com a **Microsoft.Storage/storageAccounts/listkeys/action**. Se lhe foi atribuída uma função com esta ação, então o portal utiliza a chave da conta para aceder a dados de blob e fila. Se não lhe foi atribuída uma função com esta ação, então o portal tenta aceder a dados através da sua conta AD Azure.

> [!NOTE]
> O administrador de subscrição clássico funções Administrador de Serviço e Coadministrador incluem o equivalente à função de [Proprietário do Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure. A função **Proprietário** inclui todas as ações, incluindo o **Microsoft.Storage/storageAccounts/listkeys/action,** para que um utilizador com uma destas funções administrativas também possa aceder a dados blob e fila com a chave da conta. Para mais informações, consulte as funções de administrador de [subscrição Classic](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Conta do Azure AD

Para aceder aos dados blob ou de fila do portal Azure utilizando a sua conta Azure AD, ambas as seguintes declarações devem ser verdadeiras para si:

- Foi-lhe atribuído o papel de Leitor de [Gestão](../../role-based-access-control/built-in-roles.md#reader) de Recursos Azure, no mínimo, ao nível da conta de armazenamento ou superior. O papel **do Leitor** concede as permissões mais restritas, mas outra função do Gestor de Recursos Azure que concede acesso aos recursos de gestão de contas de armazenamento também é aceitável.
- Foi-lhe atribuída uma função incorporada ou personalizada que fornece acesso a dados de blob ou fila.

A atribuição de funções do **Leitor** ou outra atribuição de funções do Gestor de Recursos Azure é necessária para que o utilizador possa visualizar e navegar recursos de gestão de contas de armazenamento no portal Azure. As funções RBAC que concedem acesso a dados blob ou de fila não concedem acesso a recursos de gestão de contas de armazenamento. Para aceder aos dados blob ou de fila no portal, o utilizador necessita de permissões para navegar nos recursos da conta de armazenamento. Para mais informações sobre esta exigência, consulte [Atribuir a função de Leitor para acesso](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)ao portal .

As funções incorporadas que suportam o acesso aos seus dados de blob ou fila incluem:

- [Armazenamento Blob Data Owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Para o controlo de acesso POSIX para O Armazenamento de Lagos Azure Data Gen2.
- [Contribuinte de dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Ler/escrever/eliminar permissões para bolhas.
- [Armazenamento Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Apenas permissões de leitura para bolhas.
- [Contribuinte](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)de dados da fila de armazenamento : Leia/escreva/apague permissões para filas.
- [Leitor de dados](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)de fila de armazenamento : Apenas leia permissões para filas.
    
As funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre a criação de funções RBAC personalizadas, consulte [funções personalizadas para recursos Azure](../../role-based-access-control/custom-roles.md) e [compreenda definições de papéis para recursos Azure.](../../role-based-access-control/role-definitions.md)

Não é suportado o anúncio de filas com uma função clássica de administrador de subscrição. Para listar as filas, um utilizador deve ter-lhes atribuído a função de Leitor de **Leitores** de Recursos Azure, a função **de Leitor** de Dados da Fila de Armazenamento ou a função de Contribuinte de Dados da Fila de **Armazenamento.**

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta utilizar credenciais azure ad para visualizar e modificar dados de blob ou fila. O Storage Explorer no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, deve ser-lhe atribuída uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegue para bolhas ou filas no portal

Para ver os dados de blob ou fila no portal, navegue para a **visão geral** para a sua conta de armazenamento e clique nos links para **Blobs** ou **Queues**. Em alternativa, pode navegar para as secções de **serviço blob** e **fila** no menu. 

![Navegue para blob ou dados de fila no portal Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando navega para um contentor ou uma fila, o portal Azure indica se está a utilizar a chave de acesso à conta ou a sua conta Azure AD para autenticar.

Os exemplos desta secção mostram o acesso a um contentor e às suas bolhas, mas o portal exibe a mesma mensagem quando está a aceder a uma fila e às suas mensagens, ou a listar filas.

### <a name="account-access-key"></a>Chave de acesso à conta

Se estiver a autenticar utilizando a chave de acesso à conta, verá a Chave de **Acesso** especificada como o método de autenticação no portal:

![Atualmente acedendo aos dados do contentor com a chave da conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para passar a utilizar a conta Azure AD, clique no link realçado na imagem. Se tiver as permissões adequadas através das funções RBAC que lhe são atribuídas, poderá proceder. No entanto, se não tiver as permissões certas, verá uma mensagem de erro como a seguinte:

![Erro mostrado se a conta Azure AD não suporta acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Note que não aparecem bolhas na lista se a sua conta Azure AD não tiver permissões para as visualizar. Clique no **switch para aceder ao link chave para** utilizar novamente a chave de acesso para autenticação.

### <a name="azure-ad-account"></a>Conta do Azure AD

Se estiver a autenticar utilizando a sua conta AD Azure, verá a Conta de **Utilizador AD Azure** especificada como o método de autenticação no portal:

![Atualmente a aceder a dados de contentores com conta Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para mudar para a utilização da chave de acesso à conta, clique no link realçado na imagem. Se tiver acesso à chave da conta, poderá prosseguir. No entanto, se não tiver acesso à chave da conta, verá uma mensagem de erro como a seguinte:

![Erro mostrado se não tiver acesso à chave da conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Note que não aparecem bolhas na lista se não tiver acesso às chaves da conta. Clique no link **Switch para Azure AD User Account** para utilizar novamente a sua conta Azure AD para autenticação.

## <a name="next-steps"></a>Passos seguintes

- [Autenticar acesso a bolhas e filas Azure usando o Diretório Ativo Azure](storage-auth-aad.md)
- [Conceder acesso a contentores e filas Azure com RBAC no portal Azure](storage-auth-aad-rbac-portal.md)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC using PowerShell](storage-auth-aad-rbac-powershell.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através do PowerShell)
