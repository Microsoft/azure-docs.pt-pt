---
title: Autenticar o acesso a blobs e filas com identidades do Azure Active Directory gerida para recursos do Azure - armazenamento do Azure | Documentos da Microsoft
description: Armazenamento de BLOBs e filas do Azure suporta a autenticação do Azure Active Directory com identidades geridas para recursos do Azure. Pode utilizar identidades geridas para recursos do Azure para autenticar o acesso a blobs e filas de aplicações em execução em máquinas virtuais do Azure, aplicações de funções, os conjuntos de dimensionamento de máquinas virtuais e outros. Ao utilizar identidades geridas para recursos do Azure e tirar partido do poder de autenticação do Azure AD, pode evitar armazenar credenciais com as aplicações que são executados na cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4372045590938df701dd00e58a111215f6e8e56d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369660"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Autenticar o acesso a blobs e filas com identidades geridas para recursos do Azure

Armazenamento de BLOBs e filas do Azure suporta a autenticação do Azure Active Directory (Azure AD) com [geridos identidades para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Gerido identidades para recursos do Azure podem autenticar o acesso a blobs e filas com credenciais do Azure AD a partir de aplicações em execução em máquinas virtuais do Azure (VMs), aplicações de funções, os conjuntos de dimensionamento de máquinas virtuais e outros. Ao utilizar identidades geridas para recursos do Azure e tirar partido do poder de autenticação do Azure AD, pode evitar armazenar credenciais com as aplicações que são executados na cloud.  

Para conceder permissões para uma identidade gerida a um contentor de BLOBs ou filas, que atribuir uma função de controlo (RBAC) de acesso baseado em funções para a identidade gerida que abrange as permissões para esse recurso com o âmbito adequado. Para obter mais informações sobre as funções do RBAC no armazenamento, consulte [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md). 

Este artigo mostra como autenticar para o armazenamento de Blobs do Azure ou a fila com uma identidade gerida a partir de uma VM do Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Ativar identidades geridas numa VM

Antes de poder utilizar identidades geridas para recursos do Azure para autenticar o acesso a blobs e filas da VM, primeiro tem de ativar identidades geridas para recursos do Azure na VM. Para saber como ativar identidades geridas para recursos do Azure, consulte um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Atribuir uma função RBAC para uma identidade do Azure AD gerido

Para autenticar uma identidade gerida a partir da sua aplicação de armazenamento do Azure, primeiro de configurar as definições de controlo (RBAC) de acesso baseado em funções para essa identidade gerida. O armazenamento do Azure define as funções RBAC que abranger permissões para contentores e filas. Quando a função RBAC é atribuída a uma identidade gerida, o que a identidade gerida é concedida acesso a esse recurso. Para obter mais informações, consulte [gerir direitos de acesso aos dados de Blobs do Azure e a fila com o RBAC](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Obter token de acesso uma identidade gerida

Para autenticar com uma identidade gerida, a sua aplicação ou script tem de adquirir um token de acesso de identidade gerida. Para saber mais sobre como adquirir um token de acesso, veja [como utilizar identidades geridas para recursos do Azure numa VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: Criar um blob de blocos

O exemplo de código parte do princípio de que tem um token de acesso de identidade gerida. O token de acesso é utilizado para autorizar a identidade gerida para criar um blob de blocos.

### <a name="add-references-and-using-statements"></a>Adicionar referências e instruções "using"  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. Do **ferramentas** menu, selecione **Gestor de pacotes Nuget**, em seguida, **Package Manager Console**. Escreva o seguinte comando na consola:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Adicione o seguinte através de instruções para o seu código:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Criar credenciais a partir do token de acesso de identidade gerida

Para criar o blob de blocos, utilize o **TokenCredentials** classe. Construir uma nova instância do **TokenCredentials**, passando o token de acesso de identidade gerida que obteve anteriormente:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> A integração do Azure AD com o armazenamento do Azure requer a utilização do HTTPS para operações de armazenamento do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso a contentores e filas de dentro dos seus aplicativos de armazenamento, veja [utilize o Azure AD com aplicações de armazenamento](storage-auth-aad-app.md).
- Para saber como iniciar sessão CLI do Azure e o PowerShell com uma identidade do Azure AD, veja [utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md).
