---
title: Caminho do blob de alteração da predefinição | Documentos da Microsoft
description: Saiba como configurar uma função do Azure para mudar o nome de um caminho de ficheiro blob
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011625"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Alterar um caminho de blob do caminho predefinido

Quando o serviço do StorSimple Data Manager transforma os dados, por padrão ele coloca os blobs transformados num contentor de armazenamento como especificado durante a criação do repositório de destino. Como os blobs chegam nesta localização, pode querer mover nestes blobs para uma localização alternativa. Este artigo descreve como configurar uma função do Azure para mudar o nome de um caminho de ficheiro de BLOBs predefinido e, por conseguinte, mover os blobs para uma localização diferente.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem uma definição de tarefa corretamente configurada no seu serviço do StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função do Azure, execute os seguintes passos:

1. Aceda ao [Portal do Azure](https://portal.azure.com/).

2. Clique em **+ criar um recurso**. Na **pesquisa** , escreva **Function App** e pressione **Enter**. Selecione e clique em **aplicação de funções** na lista de aplicações apresentada.

    ![Escreva "Aplicação de funções" na caixa de pesquisa](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Clique em **Criar**.

    ![O botão de "Criar" da janela de aplicação de função](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Sobre o **aplicação de funções** painel de configuração, execute os seguintes passos:

    1. Fornecer um exclusivo **nome da aplicação**.
    2. Na lista pendente, selecione o **subscrição**. Esta subscrição deve ser o mesmo que o associado ao seu serviço do StorSimple Data Manager.
    3. Selecione **criar novo** grupo de recursos.
    4. Para o **plano de alojamento** lista pendente, selecione **plano de consumo**.
    5. Especifique uma localização onde a função é executada. Pretende que a mesma região onde o serviço Gestor de dados do StorSimple e a conta de armazenamento associada a definição de tarefa, estão localizados.
    6. Selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é utilizada internamente para a função.

        ![Introduza novos dados de configuração de aplicação de funções](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Clique em **Criar**. A aplicação de funções é criada.
     
        ![Aplicação de funções criada](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecione **funções**e clique em **+ nova função**.

    ![Clique em + nova função](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecione **C#** para o idioma. Na matriz de mosaicos de modelo, selecione **C#** no **QueueTrigger CSharp** mosaico.

7. Na **acionador de fila**:

    1. Introduza um **nome** para a função.
    2. Na **nome da fila** , escreva o nome de definição de tarefa de transformação de dados.
    3. Sob **ligação de conta de armazenamento**, clique em **novo**. Na lista de contas de armazenamento, selecione a conta associada à sua definição de tarefa. Anote o nome da ligação (realçado). É necessário o nome mais tarde na função do Azure.

        ![Criar um novo C# função](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Clique em **Criar**. O **função** é criado.

     
10. Na janela de função, execute _. csx_ ficheiro.

    ![Criar um novo C# função](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Realize os seguintes passos.

    1. Cole o seguinte código:

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

    2. Substitua **STORAGE_CONNECTIONNAME** na linha 11 com a sua ligação de conta de armazenamento (consulte o passo 7 c).

        ![Nome de ligação de armazenamento de cópia](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Guardar** a função.

        ![Guardar a função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para concluir a função, adicione mais um arquivo efetuando os seguintes passos:

    1. Clique em **ver ficheiros**.

       ![O link "Exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Clique em **+ Adicionar**.
        
        ![O link "Exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Tipo **Project**e, em seguida, prima **Enter**. Na **Project** de ficheiros, cole o seguinte código:

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

        ![O link "Exibir arquivos"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Criou uma função do Azure. Esta função é acionada sempre que um blob novo é gerado pela tarefa de transformação de dados.

## <a name="next-steps"></a>Passos Seguintes

[Use o StorSimple Data Manager interface de Usuário para transformar os seus dados](storsimple-data-manager-ui.md)
