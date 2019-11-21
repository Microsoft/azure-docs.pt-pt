---
title: Tutorial`:` Use managed identity to access Azure Storage using SAS credential - Azure AD
description: A tutorial that shows you how to use a Windows VM system-assigned managed identity to access Azure Storage, using a SAS credential instead of a storage account access key.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c344c25a696500182030ff849a001ad586c92032
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232166"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Tutorial: Use a Windows VM system-assigned managed identity to access Azure Storage via a SAS credential

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

This tutorial shows you how to use a system-assigned identity for a Windows virtual machine (VM) to obtain a storage Shared Access Signature (SAS) credential. Especificamente, uma [credencial de SAS de Serviço](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

A Service SAS provides the ability to grant limited access to objects in a storage account, for limited time and a specific service (in our case, the blob service), without exposing an account access key. Pode utilizar uma credencial de SAS como habitualmente ao fazer operações de armazenamento, por exemplo, ao utilizar o SDK de Armazenamento. For this tutorial, we demonstrate uploading and downloading a blob using Azure Storage PowerShell. Vai aprender a:

> [!div class="checklist"]
> * Create a storage account
> * Conceder o acesso da VM a um SAS da conta de armazenamento no Resource Manager 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o SAS a partir do Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Create a storage account 

Se ainda não tiver uma, irá agora criar uma conta de armazenamento. You can also skip this step and grant your VM's system-assigned managed identity access to the SAS credential of an existing storage account. 

1. Clique no botão **+/Criar novo serviço**, no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, em seguida, em **Conta de Armazenamento**, e um novo painel "Criar a conta de armazenamento" será apresentado.
3. Introduza um nome para a conta de armazenamento, que irá utilizar mais tarde.  
4. O **Modelo de implementação** e o **Tipo de conta** devem ser definidos como "Gestor de recursos" e "Fins gerais", respetivamente. 
5. Certifique-se de que a **Subscrição** e o **Grupo de Recursos** correspondem aos perfis que especificou quando criou a VM no passo anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contentor de blobs na conta de armazenamento

Mais tarde, iremos carregar e transferir um ficheiro para a nova conta de armazenamento. Uma vez que os ficheiros requerem armazenamento de blobs, é necessário criar um contentor de blobs para armazenar o ficheiro.

1. Navegue de volta para a sua conta de armazenamento recentemente criada.
2. Clique na ligação **Contentores** no painel esquerdo, em "Serviço Blob".
3. Clique em **+ Contentor**, na parte superior da página, e surge um painel "Novo contentor".
4. Dê um nome ao contentor, selecione um nível de acesso e clique em **OK**. O nome que especificou será utilizado mais tarde no tutorial. 

    ![Criar contentor de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Conceder à identidade gerida atribuída pelo sistema da VM acesso para utilizar uma SAS de armazenamento 

O Armazenamento do Azure não suporta nativamente a autenticação do Azure AD.  However, you can use a managed identity to retrieve a storage SAS from Resource Manager, then use the SAS to access storage.  Neste passo, pode conceder à identidade gerida atribuída pelo sistema da VM acesso à SAS da sua conta de armazenamento.   

1. Navegue de volta para a sua conta de armazenamento recentemente criada.   
2. Clique na ligação **Controlo de acesso (IAM)** no painel esquerdo.  
3. Click **+ Add role assignment** on top of the page to add a new role assignment for your VM
4. Defina **Função** como "Contribuidor de Conta de Armazenamento", no lado direito da página.  
5. Na lista pendente seguinte, defina **Atribuir acesso a** ao recurso "Máquina Virtual".  
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente **Subscrição** e, em seguida, defina **Grupo de Recursos** como "Todos os grupos de recursos".  
7. Por fim, em **Selecionar** escolha a sua Máquina Virtual do Windows na lista pendente e clique em **Guardar**. 

    ![Texto alternativo da imagem](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Azure Resource Manager 

No resto do tutorial, iremos trabalhar a partir da VM que criámos anteriormente.

Terá de utilizar os cmdlets PowerShell do Azure Resource Manager nesta parte.  Se não o tiver instalado, [transfira a versão mais recente](https://docs.microsoft.com/powershell/azure/overview) antes de continuar.

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em seguida, na página **Descrição Geral**, clique em **Ligar** na parte superior.
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows. 
3. Agora que já criou uma **Ligação ao Ambiente de Trabalho Remoto** com a máquina virtual, abra o PowerShell na sessão remota. 
4. Através de Invoke-WebRequest do PowerShell, envie um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso ao Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > No pedido anterior, o valor do parâmetro "recurso" tem de ser uma correspondência exata para o que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra à direita no URI.
    
    Em seguida, extraia o elemento "Content", que é armazenado como uma cadeia formatada do JavaScript Object Notation (JSON) no objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Em seguida, extraia o token de acesso da resposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Obter uma credencial de SAS a partir do Azure Resource Manager para fazer chamadas de armazenamento 

Now use PowerShell to call Resource Manager using the access token we retrieved in the previous section, to create a storage SAS credential. Once we have the SAS credential, we can call storage operations.

Para este pedido, vamos utilizar os parâmetros do pedido de HTTP seguintes para criar a credencial de SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Estes parâmetros estão incluídos no corpo POST do pedido da credencial de SAS. Para obter mais informações sobre os parâmetros para criar uma credencial de SAS, veja [Referência do REST de SAS do Serviço de Lista](/rest/api/storagerp/storageaccounts/listservicesas).

First, convert the parameters to JSON, then call the storage `listServiceSas` endpoint to create the SAS credential:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> O URL é sensível às maiúsculas de minúsculas; por isso, certifique-se de que utiliza as mesmas maiúsculas e minúsculas que utilizou anteriormente, quando atribuiu o nome ao Grupo de Recursos, incluindo a maiúscula “G” em “resourceGroups”. 

Now we can extract the SAS credential from the response:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

If you inspect the SAS cred you'll see something like this:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Em seguida, criamos um ficheiro chamado "test.txt". Then use the SAS credential to authenticate with the `New-AzStorageContent` cmdlet, upload the file to our blob container, then download the file.

```bash
echo "This is a test text file." > test.txt
```

Não se esqueça de instalar primeiro os cmdlets de Armazenamento do Azure, com `Install-Module Azure.Storage`. Em seguida, carregue o blob que acabou de criar, com o cmdlet `Set-AzStorageBlobContent` do PowerShell:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Também pode transferir o blob que acabou de carregar, com o cmdlet `Get-AzStorageBlobContent` do PowerShell:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you learned how to use a Windows VM's system-assigned managed identity to access Azure Storage using a SAS credential.  Para saber mais sobre o SAS do Armazenamento do Azure, veja:

> [!div class="nextstepaction"]
>[Utilizar assinaturas de acesso partilhado (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


