---
title: Alterar o caminho do blob a partir do padrão
description: Aprenda a configurar uma função Azure para mudar o nome de um caminho de ficheiro blob predefinido e mover as bolhas para um local diferente.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011443"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Mudar o caminho de um blob do caminho predefinido

Quando o serviço StorSimple Data Manager transforma os dados, por padrão coloca as bolhas transformadas num recipiente de armazenamento, conforme especificado durante a criação do repositório-alvo. À medida que as bolhas chegam a este local, é melhor mover estas bolhas para um local alternativo. Este artigo descreve como configurar uma função Azure para mudar o nome de um caminho de ficheiro blob padrão e, assim, mover as bolhas para um local diferente.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem uma definição de emprego corretamente configurada no seu serviço StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função Azure, execute os seguintes passos:

1. Aceda ao [Portal do Azure](https://portal.azure.com/).

2. Clique em **+ Criar um recurso**. Na caixa **'Procurar',** **digite App function** e prima **Enter**. Selecione e clique na **aplicação Função** na lista de aplicações apresentadas.

    ![Digite "App de função" na caixa de pesquisa](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Clique em **Criar**.

    ![O botão "Criar" da janela da app de função](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na lâmina de configuração da **Aplicação de Função,** execute os seguintes passos:

    1. Forneça um nome de **aplicação** único.
    2. A partir da lista de abandono, selecione a **Subscrição**. Esta subscrição deve ser a mesma que está associada ao seu serviço StorSimple Data Manager.
    3. **Selecione Criar novo** grupo de recursos.
    4. Para a lista de abandono **do Plano de Hospedagem,** selecione **Plano de Consumo**.
    5. Especifique um local onde a sua função é executado. Pretende-se que a mesma região onde se encontra o serviço StorSimple Data Manager e a conta de armazenamento associada à definição de trabalho.
    6. Selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é utilizada internamente para a função.

        ![Introduza novos dados de configuração da App de Função](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Clique em **Criar**. A aplicação de função é criada.
     
        ![App de função criada](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecione **Funções**, e clique **+ Nova função**.

    ![Clique + Nova função](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. **Selecione C#** para o idioma. Na matriz de azulejos do modelo, selecione **C#** no azulejo **QueueTrigger-CSharp.**

7. No gatilho da **fila:**

    1. Insira um **Nome** para a sua função.
    2. Na caixa de nomes da **fila,** digite o nome da definição de trabalho de transformação de dados.
    3. Na **ligação da conta de armazenamento,** clique **em novo**. Na lista de contas de armazenamento, selecione a conta associada à definição de trabalho. Tome nota do nome da ligação (realçado). O nome é necessário mais tarde na função Azure.

        ![Criar uma nova função C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Clique em **Criar**. A **Função** é criada.

     
10. Na janela Função, executar o ficheiro _.csx._

    ![Criar uma nova função C# 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Substitua **STORAGE_CONNECTIONNAME** na linha 11 pela ligação da sua conta de armazenamento (consulte o passo 7c).

        ![Nome de conexão de armazenamento de cópia](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Salve** a função.

        ![Guardar função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para completar a função, adicione mais um ficheiro fazendo os seguintes passos:

    1. Clique **em Ver ficheiros**.

       ![O link "Ver ficheiros"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Clique em **+ Adicionar**.
        
        ![Adicionar novo ficheiro de adicionar função](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Digite **project.jse, em** seguida, prima **Enter**. Na **project.jsem** arquivo, cole o seguinte código:

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

        ![Json projeto de nova função](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Criou uma função Azure. Esta função é desencadeada cada vez que uma nova bolha é gerada pelo trabalho de transformação de dados.

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md)
