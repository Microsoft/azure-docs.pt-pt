---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Azure Storage através da chave de acesso - Linux - Azure AD
description: Um tutorial que o acompanha através do processo de utilização de uma identidade gerida pelo sistema Linux VM para aceder ao Azure Storage através de uma chave de acesso.
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
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317468"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Armazenamento do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar uma identidade atribuída pelo sistema de uma VM do Linux para recuperar as chaves de acesso à conta de armazenamento. Pode utilizar uma chave de acesso ao armazenamento como habitualmente ao fazer operações de armazenamento, por exemplo, ao utilizar o SDK de Armazenamento. Neste tutorial, vamos carregar e transferir blobs com a CLI do Azure. Vai aprender a:

> [!div class="checklist"]
> * Conceder aceder à VM a chaves de acesso da conta de armazenamento no Resource Manager 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter as chaves de acesso ao armazenamento a partir do Resource Manager  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se ainda não tiver uma, irá agora criar uma conta de armazenamento.  Também pode ignorar este passo e conceder à identidade gerida atribuída pelo sistema da VM acesso às chaves de uma conta de armazenamento existente. 

1. Clique no botão **+/Criar novo serviço**, no canto superior esquerdo do portal do Azure.
2. Clique em **Armazenamento**, em seguida, em **Conta de Armazenamento**, e um novo painel "Criar a conta de armazenamento" será apresentado.
3. Introduza um **Nome** para a conta de armazenamento, que utilizará mais tarde.  
4. O **Modelo de implementação** e o **Tipo de conta** devem ser definidos como "Gestor de recursos" e "Fins gerais", respetivamente. 
5. Certifique-se de que a **Subscrição** e o **Grupo de Recursos** correspondem aos perfis que especificou quando criou a VM no passo anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contentor de blobs na conta de armazenamento

Mais tarde, iremos carregar e transferir um ficheiro para a nova conta de armazenamento. Uma vez que os ficheiros requerem armazenamento de blobs, é necessário criar um contentor de blobs para armazenar o ficheiro.

1. Navegue de volta para a sua conta de armazenamento recentemente criada.
2. Clique na ligação **Contentores** à esquerda, em "Serviço Blob".
3. Clique em **+ Contentor**, na parte superior da página, e surge um painel "Novo contentor".
4. Dê um nome ao contentor, selecione um nível de acesso e clique em **OK**. O nome que especificou será utilizado mais tarde no tutorial. 

    ![Criar contentor de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Conceder à identidade gerida atribuída pelo sistema da VM acesso para utilizar as chaves de acesso à conta de armazenamento

Neste passo, pode conceder à identidade gerida atribuída pelo sistema da VM acesso às chaves da sua conta de armazenamento.   

1. Navegue de volta para a sua conta de armazenamento recentemente criada.
2. Clique na ligação **Controlo de acesso (IAM)** no painel esquerdo.  
3. Clique + Adicione a atribuição de **funções** no topo da página para adicionar uma nova atribuição de função para o seu VM
4. Defina a **Função** como “Função do Serviço de Operador de Chave da Conta de Armazenamento”, no lado direito da página. 
5. Na lista pendente seguinte, defina **Atribuir acesso a** ao recurso "Máquina Virtual".  
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente **Subscrição** e, em seguida, defina **Grupo de Recursos** para "Todos os grupos de recursos".  
7. Por fim, em **Selecionar**, selecione a sua Máquina Virtual do Linux na lista pendente e clique em **Guardar**. 

    ![Texto alternativo da imagem](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Azure Resource Manager

No resto do tutorial, iremos trabalhar a partir da VM que criámos anteriormente.

Para concluir estes passos, precisará de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/install-win10). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Linux e, em seguida, na página **Descrição Geral**, clique em **Ligar** na parte superior. Copie a cadeia de ligação para ligar à sua VM. 
2. Ligue à VM através do seu cliente SSH.  
3. Em seguida, ser-lhe-á pedido que introduza a **palavra-passe** que adicionou ao criar a **VM do Linux**. Deverá iniciar sessão com êxito.  
4. Utilize o CURL para obter um token de acesso para o Azure Resource Manager.  

    O pedido CURL e a resposta para o token de acesso encontram-se abaixo:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > No pedido anterior, o valor do parâmetro "recurso" tem de ser uma correspondência exata para o que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra à direita no URI.
    > Na resposta seguinte, o elemento access_token foi abreviado para não ser tão extenso.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obter chaves de acesso da conta de armazenamento do Azure Resource Manager para fazer chamadas de armazenamento  

Agora, utilize o CURL para chamar o Resource Manager com o token de acesso que obtivemos na secção anterior para obter a chave de acesso ao armazenamento. Assim que tivermos a chave de acesso ao armazenamento, podemos chamar operações de carregamento/transferência de armazenamento. Certifique-se de que substitui os valores de parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<STORAGE ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo token de acesso que obteve anteriormente:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> O texto no URL anterior é sensível às maiúsculas e minúsculas; por isso, certifique-se de que, se estiver a utilizar maiúsculas e minúsculas nos seus Grupos de Recursos, tal é refletido em conformidade. Além disso, é importante saber que se trata de um pedido POST e não de um pedido GET, e certifique-se de que passa um valor para capturar um limite de comprimento com -d que possa ser NULL.  

A resposta CURL dá-lhe a lista de Chaves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Crie um ficheiro de blob de exemplo para carregar para o seu contentor de armazenamento de blobs. Numa VM do Linux, pode fazer isto com o seguinte comando. 

```bash
echo "This is a test file." > test.txt
```

Em seguida, autentique com o comando `az storage` da CLI, utilizando a chave de acesso ao armazenamento, e carregue o ficheiro para o contentor de blobs. Para este passo, precisará de [instalar a CLI do Azure mais recente](/cli/azure/install-azure-cli) na sua VM, se ainda não o tiver feito.
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Além disso, pode transferir o ficheiro com a CLI do Azure e autenticar com a chave de acesso ao armazenamento. 

Pedido: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial aprendeu a utilizar uma identidade gerida atribuída pelo sistema da VM do Linux para aceder ao Armazenamento do Azure através de uma chave de acesso.  Para saber mais sobre as chaves de acesso ao Armazenamento do Azure, veja:

> [!div class="nextstepaction"]
>[Gerir as chaves de acesso ao armazenamento](../../storage/common/storage-account-create.md)
