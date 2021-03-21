---
title: '`:`Tutorial Utilização gerida identidade para aceder ao Armazenamento Azure usando credencial SAS - Azure AD'
description: Um tutorial que mostra como usar uma identidade gerida pelo sistema Windows VM para aceder ao Azure Storage, utilizando uma credencial SAS em vez de uma chave de acesso à conta de armazenamento.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b4a6f7915f931e2eff24b56b178957a039e1ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101096592"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Tutorial: Utilize uma identidade gerida atribuída ao sistema Windows VM para aceder ao Azure Storage através de uma credencial SAS

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como usar uma identidade atribuída ao sistema para uma máquina virtual do Windows (VM) para obter uma credencial de assinatura de acesso partilhado (SAS) de armazenamento. Especificamente, uma [credencial de SAS de Serviço](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

A Service SAS oferece a capacidade de conceder acesso limitado a objetos numa conta de armazenamento, por tempo limitado e um serviço específico (no nosso caso, o serviço blob), sem expor uma chave de acesso à conta. Pode utilizar uma credencial de SAS como habitualmente ao fazer operações de armazenamento, por exemplo, ao utilizar o SDK de Armazenamento. Para este tutorial, demonstramos o upload e o download de uma bolha usando o Azure Storage PowerShell. Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Conceder o acesso da VM a um SAS da conta de armazenamento no Resource Manager 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o SAS a partir do Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão das identidades geridas. Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Permissões "Proprietário" no âmbito apropriado (a sua subscrição ou grupo de recursos) para executar as etapas necessárias de criação de recursos e gestão de funções. Se precisar de assistência com a atribuição de funções, consulte [as funções de Assign Azure para gerir o acesso aos seus recursos de subscrição Azure](../../role-based-access-control/role-assignments-portal.md).
- Também precisa de uma máquina Virtual do Windows que tenha o sistema atribuído a identidades geridas ativadas.
  - Se precisar de criar uma máquina virtual para este tutorial, pode seguir o artigo intitulado [Criar uma máquina virtual com identidade atribuída ao sistema ativada](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se ainda não tiver uma, irá agora criar uma conta de armazenamento. Também pode saltar este passo e conceder o acesso de identidade gerido ao sistema atribuído ao credencial SAS de uma conta de armazenamento existente. 

1. Clique no botão **+/Criar novo serviço**, no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, em seguida, em **Conta de Armazenamento**, e um novo painel "Criar a conta de armazenamento" será apresentado.
3. Introduza um nome para a conta de armazenamento, que irá utilizar mais tarde.  
4. **O modelo** de implantação e **o tipo de Conta** devem ser definidos como "Gestor de Recursos" e "Propósito Geral", respectivamente. 
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

O Armazenamento do Azure não suporta nativamente a autenticação do Azure AD.  No entanto, pode utilizar uma identidade gerida para recuperar um SAS de armazenamento do Gestor de Recursos e, em seguida, usar o SAS para aceder ao armazenamento.  Neste passo, pode conceder à identidade gerida atribuída pelo sistema da VM acesso à SAS da sua conta de armazenamento.   

1. Navegue de volta para a sua conta de armazenamento recentemente criada.   
2. Clique na ligação **Controlo de acesso (IAM)** no painel esquerdo.  
3. Clique + Adicione a atribuição de **funções** no topo da página para adicionar uma nova atribuição de função para o seu VM
4. Defina **Função** como "Contribuidor de Conta de Armazenamento", no lado direito da página.  
5. Na lista pendente seguinte, defina **Atribuir acesso a** ao recurso "Máquina Virtual".  
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente **Subscrição** e, em seguida, defina **Grupo de Recursos** para "Todos os grupos de recursos".  
7. Por fim, em **Selecionar** escolha a sua Máquina Virtual do Windows na lista pendente e clique em **Guardar**. 

    ![Texto alternativo da imagem](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Azure Resource Manager 

Para o resto do tutorial, trabalharemos a partir do seu VM.

Terá de utilizar os cmdlets PowerShell do Azure Resource Manager nesta parte.  Se não o tiver instalado, [faça o download da versão mais recente](/powershell/azure/) antes de continuar.

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em seguida, na página **Descrição Geral**, clique em **Ligar** na parte superior.
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows. 
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual.
4. Abra a PowerShell na sessão remota e use Invoke-WebRequest para obter um token Azure Resource Manager da identidade gerida local para o ponto final dos recursos Azure.

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

Agora use o PowerShell para ligar para o Gestor de Recursos usando o token de acesso que recuperamos na secção anterior, para criar uma credencial SAS de armazenamento. Assim que tivermos a credencial SAS, podemos ligar para as operações de armazenamento.

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

Primeiro, converta os parâmetros para JSON e, em seguida, chame o ponto final de armazenamento `listServiceSas` para criar a credencial SAS:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> O URL é sensível às maiúsculas de minúsculas; por isso, certifique-se de que utiliza as mesmas maiúsculas e minúsculas que utilizou anteriormente, quando atribuiu o nome ao Grupo de Recursos, incluindo a maiúscula “G” em “resourceGroups”. 

Agora podemos extrair a credencial SAS da resposta:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Se inspecionar o crédito da SAS, verá algo assim:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Em seguida, criamos um ficheiro chamado "test.txt". Em seguida, utilize a credencial SAS para autenticar com o `New-AzStorageContent` cmdlet, faça o upload do ficheiro para o nosso recipiente blob e, em seguida, descarregue o ficheiro.

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

Neste tutorial, aprendeu a usar a identidade gerida de um Windows VM para aceder ao Azure Storage usando uma credencial SAS.  Para saber mais sobre o SAS do Armazenamento do Azure, veja:

> [!div class="nextstepaction"]
>[Utilizar assinaturas de acesso partilhado (SAS)](../../storage/common/storage-sas-overview.md)