---
title: Utilize o portal Azure para aceder a dados de blob ou fila
titleSuffix: Azure Storage
description: Ao aceder a dados de blob ou de fila utilizando o portal Azure, o portal faz pedidos para o Azure Storage sob as capas. Estes pedidos para o Azure Storage podem ser autenticados e autorizados usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9b2de4f320801b20de5bcc9687a723dadb182ef8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807710"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Utilize o portal Azure para aceder a dados de blob ou fila

Ao aceder a dados de blob ou de fila utilizando o [portal Azure,](https://portal.azure.com)o portal faz pedidos para o Azure Storage sob as capas. Um pedido para a Azure Storage pode ser autorizado usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento. O portal indica qual o método que está a utilizar e permite-lhe alternar entre os dois se tiver as permissões adequadas.  

Também pode especificar como autorizar uma operação de upload individual de blob no portal Azure. Por predefinição, o portal utiliza qualquer método que já esteja a utilizar para autorizar uma operação de upload de bolhas, mas tem a opção de alterar esta definição quando fizer o upload de uma bolha.

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para aceder a dados blob ou de fila

Dependendo da forma como pretende autorizar o acesso a dados de blob ou de fila no portal Azure, necessitará de permissões específicas. Na maioria dos casos, estas permissões são fornecidas através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre o RBAC, veja [o que é o controlo de acesso baseado em funções (RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Use a chave de acesso à conta

Para aceder aos dados blob e de fila com a chave de acesso à conta, tem de ter uma função RBAC que inclui a ação RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Este papel rbac pode ser um papel incorporado ou personalizado. As funções incorporadas que suportam **o Microsoft.Storage/storageAstas/listkeys/ação** incluem:

- A função [de proprietário](../../role-based-access-control/built-in-roles.md#owner) de recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) do Gestor de Recursos Azure
- A [função de contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando tenta aceder a dados de blob ou de fila no portal Azure, o portal verifica primeiro se lhe foi atribuída uma função com **o Microsoft.Storage/storageAcounts/listkeys/action**. Se lhe foi atribuído um papel com esta ação, então o portal utiliza a chave de conta para aceder a dados de blob e fila. Se não lhe foi atribuído um papel com esta ação, então o portal tenta aceder aos dados utilizando a sua conta AZure AD.

> [!NOTE]
> As funções clássicas de administrador de subscrição Administrador e Coadministrador incluem o equivalente à função de [Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure. A função **'Proprietário'** inclui todas as ações, incluindo o **Microsoft.Storage/storageAccounts/listkeys/action,** para que um utilizador com uma destas funções administrativas também possa aceder a dados de blob e fila com a chave da conta. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de administrador do Azure RBAC e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Use a sua conta AZure AD

Para aceder a dados de blob ou fila a partir do portal Azure utilizando a sua conta Azure AD, ambas as seguintes declarações devem ser verdadeiras para si:

- Foi-lhe atribuída a função de [Leitor](../../role-based-access-control/built-in-roles.md#reader) de Gestor de Recursos Azure, no mínimo, ao nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gestor de Recursos Azure que concede acesso aos recursos de gestão de conta de armazenamento também é aceitável.
- Foi-lhe atribuído um papel incorporado ou personalizado que dá acesso a dados de bolhas ou filas.

A atribuição de funções **de Reader** ou outra atribuição de função do Gestor de Recursos Azure é necessária para que o utilizador possa visualizar e navegar recursos de gestão de conta de armazenamento no portal Azure. As funções do RBAC que concedem acesso a dados de blob ou de fila não concedem acesso aos recursos de gestão de conta de armazenamento. Para aceder a dados de blob ou fila no portal, o utilizador necessita de permissões para navegar nos recursos da conta de armazenamento. Para obter mais informações sobre este requisito, consulte [atribuir a função Reader para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções incorporadas que suportam o acesso ao seu blob ou dados de fila incluem:

- [Proprietário de dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Para o controlo de acesso POSIX para Azure Data Lake Storage Gen2.
- [Colaborador de dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Ler/escrever/eliminar permissões para bolhas.
- [Armazenamento Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Permissões só de leitura para bolhas.
- [Colaborador de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Leia/escreva/elimine permissões para filas.
- [Leitor de dados de fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Permissões apenas de leitura para filas.

As funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre a criação de funções personalizadas de RBAC, consulte [os papéis personalizados para os recursos Azure](../../role-based-access-control/custom-roles.md) e [compreenda definições de funções para recursos Azure.](../../role-based-access-control/role-definitions.md)

A listagem de filas com uma função clássica de administrador de subscrição não é suportada. Para listar as filas, um utilizador deve ter-lhes atribuído a função de **Leitor** de Gestor de Recursos Azure, a função **de Leitor de Dados de Fila de Armazenamento** ou a função de Contribuinte de **Dados de Fila de Armazenamento.**

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta a utilização de credenciais AZure AD para visualizar e modificar dados de blob ou fila. O Explorador de Armazenamento no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, tem de ser atribuída uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegue para bolhas ou filas no portal

Para ver os dados de blob ou fila no portal, navegue na **Visão Geral** para a sua conta de armazenamento e clique nos links para **Blobs** ou **Filas**. Em alternativa, pode navegar para as secções de serviço **Blob** e **Queue** no menu. 

![Navegue para os dados blob ou de fila no portal Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando navega para um contentor ou uma fila, o portal Azure indica se está atualmente a utilizar a chave de acesso à conta ou a sua conta AZure AD para autenticar.

Os exemplos nesta secção mostram o acesso a um contentor e às suas bolhas, mas o portal exibe a mesma mensagem quando está a aceder a uma fila e às suas mensagens, ou a listar filas.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso à conta

Se estiver a autenticar utilizando a chave de acesso à conta, verá a **Chave de Acesso** especificada como o método de autenticação no portal:

![Atualmente a aceder aos dados dos contentores com a chave da conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para mudar para a conta Azure AD, clique no link realçado na imagem. Se tiver as permissões apropriadas através das funções RBAC que lhe são atribuídas, poderá prosseguir. No entanto, se não tiver as permissões certas, verá uma mensagem de erro como a seguinte:

![Erro mostrado se a conta AD Azure não suporta acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Note que não aparecem bolhas na lista se a sua conta AZure AD não tiver permissões para as visualizar. Clique no **Switch para aceder ao** link chave para utilizar novamente a chave de acesso para autenticação.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com a sua conta AZure AD

Se estiver a autenticar utilizando a sua conta Azure AD, verá **a Conta de Utilizador AZure AD** especificada como o método de autenticação no portal:

![Atualmente a aceder aos dados dos contentores com conta Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para mudar para a utilização da chave de acesso à conta, clique no link realçado na imagem. Se tiver acesso à chave da conta, poderá prosseguir. No entanto, se não tiver acesso à chave da conta, verá uma mensagem de erro como a seguinte:

![Erro mostrado se não tiver acesso à chave da conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Note que não aparecem bolhas na lista se não tiver acesso às chaves da conta. Clique no link **'Conta de Utilizador AD' Azure** para utilizar novamente a sua conta Azure AD para autenticação.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Especificar como autorizar uma operação de upload de blob

Ao carregar uma bolha a partir do portal Azure, pode especificar se autentica e autorizar essa operação com a chave de acesso à conta ou com as suas credenciais AD AZure. Por predefinição, o portal utiliza o método de autenticação atual, tal como mostrado no [Método de Autenticação Atual](#determine-the-current-authentication-method).

Para especificar como autorizar uma operação de upload de bolhas, siga estes passos:

1. No portal Azure, navegue até ao recipiente onde deseja carregar uma bolha.
1. Selecione o botão **Carregar**.
1. Expanda a secção **Advanced** para exibir as propriedades avançadas para a bolha.
1. No campo **Tipo autenticação,** indique se pretende autorizar a operação de upload utilizando a sua conta AZure AD ou com a chave de acesso à conta, como mostra a seguinte imagem:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="Screenshot mostrando como alterar método de autorização no upload de blob":::

## <a name="next-steps"></a>Próximos passos

- [Autenticar acesso a bolhas e filas Azure usando o Azure Ative Directory](storage-auth-aad.md)
- [Conceder acesso a contentores e filas Azure com RBAC no portal Azure](storage-auth-aad-rbac-portal.md)
- [Grant access to Azure blob and queue data with RBAC using Azure CLI](storage-auth-aad-rbac-cli.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através da CLI do Azure)
- [Grant access to Azure blob and queue data with RBAC using PowerShell](storage-auth-aad-rbac-powershell.md) (Conceder acesso a dados de blobs e filas do Azure com RBAC através do PowerShell)
