---
title: Escolha como autorizar o acesso aos dados blob no portal Azure
titleSuffix: Azure Storage
description: Ao aceder aos dados blob utilizando o portal Azure, o portal faz pedidos para o Azure Storage sob as capas. Estes pedidos para o Azure Storage podem ser autenticados e autorizados usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperfq1
ms.openlocfilehash: 5b7d2c05df71e77d623aeb8a3842851db208f691
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637425"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Escolha como autorizar o acesso aos dados blob no portal Azure

Ao aceder aos dados blob utilizando o [portal Azure,](https://portal.azure.com)o portal faz pedidos para o Azure Storage sob as capas. Um pedido para a Azure Storage pode ser autorizado usando a sua conta AZure AD ou a chave de acesso à conta de armazenamento. O portal indica qual o método que está a utilizar e permite-lhe alternar entre os dois se tiver as permissões adequadas.  

Também pode especificar como autorizar uma operação de upload individual de blob no portal Azure. Por predefinição, o portal utiliza qualquer método que já esteja a utilizar para autorizar uma operação de upload de bolhas, mas tem a opção de alterar esta definição quando fizer o upload de uma bolha.

## <a name="permissions-needed-to-access-blob-data"></a>Permissões necessárias para aceder aos dados blob

Dependendo da forma como pretende autorizar o acesso aos dados blob no portal Azure, necessitará de permissões específicas. Na maioria dos casos, estas permissões são fornecidas através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre o Azure RBAC, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Use a chave de acesso à conta

Para aceder aos dados blob com a chave de acesso à conta, tem de ter uma função Azure que inclui a ação Azure RBAC **Microsoft.Storage/storageAcounts/listkeys/action**. Este papel de Azure pode ser um papel incorporado ou personalizado. As funções incorporadas que suportam **o Microsoft.Storage/storageAstas/listkeys/ação** incluem:

- A função [de proprietário](../../role-based-access-control/built-in-roles.md#owner) de recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) do Gestor de Recursos Azure
- A [função de contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Quando tenta aceder aos dados blob no portal Azure, o portal verifica primeiro se lhe foi atribuída uma função com **o Microsoft.Storage/storageAcounts/listkeys/action**. Se lhe foi atribuído um papel com esta ação, então o portal utiliza a chave de conta para aceder aos dados blob. Se não lhe foi atribuído um papel com esta ação, então o portal tenta aceder aos dados utilizando a sua conta AZure AD.

> [!NOTE]
> As funções clássicas de administrador de subscrição Administrador de serviço e Co-Administrator incluem o equivalente à função de [Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure. A função **'Proprietário'** inclui todas as ações, incluindo a **Microsoft.Storage/storageAccounts/listkeys/action,** para que um utilizador com uma destas funções administrativas também possa aceder aos dados blob com a chave da conta. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Use a sua conta AZure AD

Para aceder aos dados blob do portal Azure utilizando a sua conta Azure AD, ambas as seguintes declarações devem ser verdadeiras para si:

- Foi-lhe atribuída a função de [Leitor](../../role-based-access-control/built-in-roles.md#reader) de Gestor de Recursos Azure, no mínimo, ao nível da conta de armazenamento ou superior. A função **Reader** concede as permissões mais restritas, mas outra função de Gestor de Recursos Azure que concede acesso aos recursos de gestão de conta de armazenamento também é aceitável.
- Foi-lhe atribuído um papel incorporado ou personalizado que dá acesso a dados blob.

A atribuição de funções **de Reader** ou outra atribuição de função do Gestor de Recursos Azure é necessária para que o utilizador possa visualizar e navegar recursos de gestão de conta de armazenamento no portal Azure. As funções Azure que concedem acesso a dados blob não concedem acesso aos recursos de gestão de conta de armazenamento. Para aceder aos dados blob no portal, o utilizador necessita de permissões para navegar nos recursos da conta de armazenamento. Para obter mais informações sobre este requisito, consulte [atribuir a função Reader para acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções incorporadas que suportam o acesso aos seus dados blob incluem:

- [Proprietário de dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Para o controlo de acesso POSIX para Azure Data Lake Storage Gen2.
- [Colaborador de dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Ler/escrever/eliminar permissões para bolhas.
- [Armazenamento Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Permissões só de leitura para bolhas.

As funções personalizadas podem suportar diferentes combinações das mesmas permissões fornecidas pelas funções incorporadas. Para obter mais informações sobre a criação de funções personalizadas [Azure, consulte os papéis personalizados da Azure](../../role-based-access-control/custom-roles.md) e [compreenda definições de funções para recursos Azure.](../../role-based-access-control/role-definitions.md)

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta a utilização de credenciais AZure AD para visualizar e modificar dados blob. O Explorador de Armazenamento no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, tem de ser atribuída uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navegue para bolhas no portal Azure

Para ver os dados blob no portal, navegue na **Visão Geral** para a sua conta de armazenamento e clique nos links para **Blobs**. Em alternativa, pode navegar para a secção **de serviço Blob** no menu.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot mostrando como navegar para os dados blob no portal Azure":::

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando navega para um contentor, o portal Azure indica se está a utilizar a chave de acesso à conta ou a sua conta AZure AD para autenticar.

### <a name="authenticate-with-the-account-access-key"></a>Autenticar com a chave de acesso à conta

Se estiver a autenticar utilizando a chave de acesso à conta, verá a **Chave de Acesso** especificada como o método de autenticação no portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Screenshot mostrando o utilizador que atualmente acede a contentores com a chave da conta":::

Para mudar para a conta Azure AD, clique no link realçado na imagem. Se tiver as permissões apropriadas através das funções Azure que lhe são atribuídas, poderá prosseguir. No entanto, se não tiver as permissões certas, verá uma mensagem de erro como a seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Erro mostrado se a conta AD Azure não suporta acesso":::

Note que não aparecem bolhas na lista se a sua conta AZure AD não tiver permissões para as visualizar. Clique no **Switch para aceder ao** link chave para utilizar novamente a chave de acesso para autenticação.

### <a name="authenticate-with-your-azure-ad-account"></a>Autenticar com a sua conta AZure AD

Se estiver a autenticar utilizando a sua conta Azure AD, verá **a Conta de Utilizador AZure AD** especificada como o método de autenticação no portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Screenshot mostrando o utilizador que atualmente acede a contentores com conta Azure AD":::

Para mudar para a utilização da chave de acesso à conta, clique no link realçado na imagem. Se tiver acesso à chave da conta, poderá prosseguir. No entanto, se não tiver acesso à chave da conta, verá uma mensagem de erro como a seguinte:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Erro mostrado se não tiver acesso à chave da conta":::

Note que não aparecem bolhas na lista se não tiver acesso às chaves da conta. Clique no link **'Conta de Utilizador AD' Azure** para utilizar novamente a sua conta Azure AD para autenticação.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Especificar como autorizar uma operação de upload de blob

Ao carregar uma bolha a partir do portal Azure, pode especificar se autentica e autorizar essa operação com a chave de acesso à conta ou com as suas credenciais AD AZure. Por predefinição, o portal utiliza o método de autenticação atual, tal como mostrado no [Método de Autenticação Atual](#determine-the-current-authentication-method).

Para especificar como autorizar uma operação de upload de bolhas, siga estes passos:

1. No portal Azure, navegue até ao recipiente onde deseja carregar uma bolha.
1. Selecione o botão **Carregar**.
1. Expanda a secção **Advanced** para exibir as propriedades avançadas para a bolha.
1. No campo **Tipo autenticação,** indique se pretende autorizar a operação de upload utilizando a sua conta AZure AD ou com a chave de acesso à conta, como mostra a seguinte imagem:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Screenshot mostrando como alterar método de autorização no upload de blob":::

## <a name="next-steps"></a>Passos seguintes

- [Autenticar acesso a bolhas e filas Azure usando o Azure Ative Directory](../common/storage-auth-aad.md)
- [Utilize o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-portal.md)
- [Utilize o CLI Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-cli.md)
- [Utilize o módulo Azure PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-powershell.md)
