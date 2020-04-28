---
title: Alterar o caminho blob a partir do padrão
description: Aprenda a configurar uma função Azure para renomear um caminho de ficheiro blob
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76270618"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Mude um caminho blob do caminho padrão

Quando o serviço StorSimple Data Manager transforma os dados, por padrão coloca as bolhas transformadas num recipiente de armazenamento conforme especificado durante a criação do repositório-alvo. À medida que as bolhas chegam a este local, é possível que queira mover estas bolhas para um local alternativo. Este artigo descreve como configurar uma função Azure para renomear um caminho de ficheiro blob padrão e, portanto, mover as bolhas para um local diferente.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem uma definição de trabalho corretamente configurada no seu serviço StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função Azure, execute os seguintes passos:

1. Vá ao [portal Azure.](https://portal.azure.com/)

2. Clique **+ Criar um recurso**. Na caixa **de pesquisa,** digite **a App função** e prima **Enter**. Selecione e clique na **aplicação Função** na lista de aplicações apresentadas.

    ![Digite "App de Função" na caixa de pesquisa](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Clique em **Criar**.

    ![O botão "Criar" da janela da aplicação de funções](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na lâmina de configuração da **Aplicação de Funções,** execute os seguintes passos:

    1. Forneça um nome de **Aplicativo**único.
    2. A partir da lista de abandono, selecione a **Subscrição**. Esta subscrição deve ser a mesma que está associada ao seu serviço StorSimple Data Manager.
    3. Selecione **Criar um novo** grupo de recursos.
    4. Para a lista de dropdown do **Plano de Hospedagem,** selecione **Plano de Consumo**.
    5. Especifique um local onde a sua função funciona. Deseja a mesma região onde estão localizados o serviço StorSimple Data Manager e a conta de armazenamento associada à definição de emprego.
    6. Selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é utilizada internamente para a função.

        ![Introduza novos dados de configuração da App de Funções](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Clique em **Criar**. A aplicação de funções é criada.
     
        ![App de funções criada](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecione **Funções,** clique **+ Nova função**.

    ![Clique + Nova função](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecione **C#** para o idioma. Na matriz de azulejos de modelo, selecione **C#** no azulejo **QueueTrigger-CSharp.**

7. No gatilho da **fila:**

    1. Introduza um **Nome** para a sua função.
    2. Na caixa de **nome** sinuoso, digite o nome da definição de emprego de transformação de dados.
    3. Sob **a ligação da conta de armazenamento,** clique **em novo**. A partir da lista de contas de armazenamento, selecione a conta associada à sua definição de trabalho. Tome nota do nome de ligação (realçado). O nome é necessário mais tarde na função Azure.

        ![Criar uma nova função C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Clique em **Criar**. A **Função** é criada.

     
10. Na janela Função, executar _ficheiro .csx._

    ![Criar uma nova função C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Realize os seguintes passos.

    1. Colar o seguinte código:

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

    2. Substitua **STORAGE_CONNECTIONNAME** na linha 11 com a ligação da sua conta de armazenamento (consulte o passo 7c).

        ![Copiar nome de ligação de armazenamento](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Salve** a função.

        ![Salvar a função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para completar a função, adicione mais um ficheiro fazendo os seguintes passos:

    1. Clique em **ver ficheiros**.

       ![O link "Ver ficheiros"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Clique em **+ Adicionar**.
        
        ![O link "Ver ficheiros"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Digite **projeto.json**e, em seguida, prima **Enter**. No ficheiro **project.json,** cola o seguinte código:

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

        ![O link "Ver ficheiros"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Criou uma função Azure. Esta função é desencadeada cada vez que uma nova bolha é gerada pelo trabalho de transformação de dados.

## <a name="next-steps"></a>Passos seguintes

[Use o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md)
