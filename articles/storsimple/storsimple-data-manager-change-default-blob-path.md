---
title: Alterar o caminho do blob do padrão
description: Saiba como configurar uma função do Azure para renomear um caminho de arquivo de BLOB
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270618"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Alterar um caminho de blob do caminho padrão

Quando o serviço de Gerenciador de Dados do StorSimple transforma os dados, por padrão ele coloca os BLOBs transformados em um contêiner de armazenamento conforme especificado durante a criação do repositório de destino. À medida que os BLOBs chegam nesse local, convém mover esses BLOBs para um local alternativo. Este artigo descreve como configurar uma função do Azure para renomear um caminho de arquivo de blob padrão e, portanto, mover os BLOBs para um local diferente.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma definição de trabalho configurada corretamente em seu serviço de Gerenciador de Dados do StorSimple.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função do Azure, execute as seguintes etapas:

1. Aceda ao [Portal do Azure](https://portal.azure.com/).

2. Clique em **+ criar um recurso**. Na caixa de **pesquisa** , digite **aplicativo de funções** e pressione **Enter**. Selecione e clique em **aplicativo de funções** na lista de aplicativos exibida.

    ![Digite "Aplicativo de funções" na caixa de pesquisa](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Clique em **Criar**.

    ![O botão "criar" da janela de Aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na folha configuração do **aplicativo de funções** , execute as seguintes etapas:

    1. Forneça um **nome de aplicativo**exclusivo.
    2. Na lista suspensa, selecione a **assinatura**. Essa assinatura deve ser a mesma associada ao seu serviço de Gerenciador de Dados do StorSimple.
    3. Selecione **criar novo** grupo de recursos.
    4. Para a lista suspensa **plano de hospedagem** , selecione **plano de consumo**.
    5. Especifique um local onde sua função é executada. Você deseja a mesma região em que o serviço de Gerenciador de Dados do StorSimple e a conta de armazenamento associada à definição de trabalho estão localizados.
    6. Selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é usada internamente para a função.

        ![Inserir novos dados de configuração de Aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Clique em **Criar**. O aplicativo de funções é criado.
     
        ![Aplicativo de funções criado](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecione **funções**e clique em **+ nova função**.

    ![Clique em + nova função](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecione **C#** para o idioma. Na matriz de blocos de modelo, selecione **C#** no bloco **QueueTrigger-Csharp** .

7. No **gatilho de fila**:

    1. Insira um **nome** para a função.
    2. Na caixa **nome da fila** , digite o nome da definição do trabalho de transformação de dados.
    3. Em **conexão da conta de armazenamento**, clique em **novo**. Na lista de contas de armazenamento, selecione a conta associada à sua definição de trabalho. Anote o nome da conexão (realçado). O nome é necessário posteriormente na função do Azure.

        ![Criar uma nova C# função](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Clique em **Criar**. A **função** é criada.

     
10. Na janela da função, execute o arquivo _. CSX_ .

    ![Criar uma nova C# função](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Realize os seguintes passos.

    1. Cole o código a seguir:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Substitua **STORAGE_CONNECTIONNAME** na linha 11 pela conexão da conta de armazenamento (consulte a etapa 7c).

        ![Copiar nome da conexão de armazenamento](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Salve** a função.

        ![Salvar função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para concluir a função, adicione mais um arquivo executando as seguintes etapas:

    1. Clique em **Exibir arquivos**.

       ![O link "exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Clique em **+ Adicionar**.
        
        ![O link "exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Digite **Project. JSON**e pressione **Enter**. No arquivo **Project. JSON** , Cole o seguinte código:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Clique em **Guardar**.

        ![O link "exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Você criou uma função do Azure. Essa função é disparada cada vez que um novo BLOB é gerado pelo trabalho de transformação de dados.

## <a name="next-steps"></a>Passos seguintes

[Use Gerenciador de Dados do StorSimple interface do usuário para transformar seus dados](storsimple-data-manager-ui.md)
