---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Azure Storage - Linux - Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema de uma VM do Linux, para aceder ao Armazenamento do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b61d3ed21d053fc7166b47c94a9ec61e355d199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89263166"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Armazenamento do Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar uma identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Linux para aceder ao Armazenamento do Azure. Saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contentor de blobs numa conta de armazenamento
> * Conceder acesso à Identidade Gerida de VM do Linux a um contentor de Armazenamento do Azure
> * Obter um token de acesso e utilizá-lo para chamar o Armazenamento do Azure

> [!NOTE]
> A autenticação do Active Directory do Azure para o Armazenamento do Azure está em pré-visualização pública.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Para executar os exemplos de script da CLI neste tutorial, tem duas opções:

- Utilizar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md) do portal do Azure ou através do botão **Experimentar**, localizado no canto superior direito de cada bloco de código.
- [Instalar a versão mais recente da CLI 2.0](/cli/azure/install-azure-cli) (2.0.23 ou posterior), se preferir utilizar uma consola CLI local.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Nesta secção, vai criar uma conta de armazenamento. 

1. Clique no botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** e, em seguida, **Conta de armazenamento – blob, ficheiro, tabela, fila**.
3. Em **Nome**, introduza um nome para a conta de armazenamento.  
4. O **Modelo de implementação** e o **Tipo de conta** devem ser definidos como **Gestor de recursos** e **Armazenamento (fins gerais v1)**. 
5. Certifique-se de que a **Subscrição** e o **Grupo de Recursos** correspondem aos perfis que especificou quando criou a VM no passo anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Criar um contentor de blobs e carregar um ficheiro para a conta de armazenamento

Os ficheiros requerem armazenamento de blobs, por isso tem de criar um contentor de blobs para armazenar o ficheiro. Em seguida, carregue um ficheiro para o contentor de blobs na nova conta de armazenamento.

1. Navegue de volta para a sua conta de armazenamento recentemente criada.
2. No **serviço Blob,** clique em **Recipientes**.
3. Clique em **+ Contentor** na parte superior da página.
4. Em **Novo contentor**, introduza um nome para o contentor e, em **Nível de acesso público**, mantenha o valor predefinido.

    ![Criar contentor de armazenamento](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Com um editor à sua escolha, crie um ficheiro com o nome *hello world.txt* no seu computador local.  Abra o ficheiro e adicione o texto (sem as aspas) "Hello world! :) "e, em seguida, guarde-o. 

6. Carregue o ficheiro para o contentor recentemente criado ao clicar no nome do contentor e, em seguida, em **Carregar**
7. No painel **Carregar blob**, em **Ficheiros**, clique no ícone de pasta e procure o ficheiro **hello_world.txt** no seu computador local, selecione o ficheiro e, em seguida, clique em **Carregar**.

    ![Carregar ficheiro de texto](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Conceder o acesso da VM a um contentor do Armazenamento do Microsoft Azure 

Pode utilizar a identidade gerida da VM para obter os dados no blob de armazenamento do Azure.   

1. Navegue de volta para a sua conta de armazenamento recentemente criada.  
2. Clique na ligação **Controlo de acesso (IAM)** no painel esquerdo.  
3. Clique + Adicione a atribuição de **funções** no topo da página para adicionar uma nova atribuição de função para o seu VM.
4. Under **Role**, a partir do dropdown, selecione **Storage Blob Data Reader**. 
5. Na lista pendente seguinte, em **Atribuir acesso a**, selecione **Máquina Virtual**.  
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente **Subscrição** e, em seguida, defina **Grupo de Recursos** para **Todos os grupos de recursos**.  
7. Em **Selecionar**, selecione a VM e, em seguida, clique em **Guardar**.

    ![Atribuir permissões](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Obter um token de acesso e utilizá-lo para chamar o Armazenamento do Azure

O Armazenamento do Azure suporta nativamente a autenticação do Azure AD, para que possa aceitar diretamente tokens de acesso obtidos com uma Identidade Gerida. Isto faz parte da integração do Armazenamento do Azure no Azure AD e é diferente de fornecer as credenciais na cadeia de ligação.

Para concluir os passos seguintes, precisa de trabalhar a partir da VM que criou anteriormente e é necessário um cliente SSH para ligar à mesma. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Linux e, em seguida, na página **Descrição Geral**, clique em **Ligar**. Copie a cadeia de ligação para ligar à sua VM.
2. Clique em **Ligar** para ligar à VM com o cliente SSH que escolheu. 
3. Na janela de terminal, com o CURL, faça um pedido ao ponto final da Identidade Gerida local para obter um token de acesso para o Armazenamento do Azure.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Utilize o token de acesso para aceder ao Armazenamento do Azure, por exemplo, para ler o conteúdo do ficheiro de exemplo que carregou anteriormente para o contentor. Substitua os valores `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` e `<FILE NAME>` pelos valores que especificou anteriormente, e `<ACCESS TOKEN>` pelo token devolvido no passo anterior.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A resposta inclui o conteúdo do ficheiro:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ativar uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Armazenamento do Azure.  Para saber mais sobre o Armazenamento do Azure, veja:

> [!div class="nextstepaction"]
> [Armazenamento do Azure](../../storage/common/storage-introduction.md)