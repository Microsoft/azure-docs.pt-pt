---
title: Exporte e importação De Centros de Notificação Azure registos a granel [ Export and importe Hubs] registos a granel Microsoft Docs
description: Saiba como utilizar o suporte a granel dos Centros de Notificação para realizar um grande número de operações num centro de notificação ou para exportar todos os registos.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71212392"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Exporte e importação De Centros de Notificação Azure registos a granel
Existem cenários em que é necessário criar ou modificar um grande número de registos num centro de notificação. Alguns destes cenários são atualizações de etiquetas após computações de lote, ou migração de uma implementação de impulso existente para usar Centros de Notificação.

Este artigo explica como realizar um grande número de operações num centro de notificação, ou para exportar todos os registos, a granel.

## <a name="high-level-flow"></a>Fluxo de alto nível
O apoio ao lote destina-se a apoiar postos de trabalho de longa duração envolvendo milhões de registos. Para atingir esta escala, o suporte de lote utiliza o Armazenamento Azure para armazenar detalhes de trabalho e saída. Para operações de atualização a granel, o utilizador é obrigado a criar um ficheiro num contentor blob, cujo conteúdo é a lista de operações de atualização de registo. Ao iniciar o trabalho, o utilizador fornece um URL à bolha de entrada, juntamente com um URL para um diretório de saída (também num recipiente de bolhas). Após o início do trabalho, o utilizador pode verificar o estado consultando uma localização url fornecida no início do trabalho. Um trabalho específico só pode realizar operações de tipo específico (cria, atualiza ou elimina). As operações de exportação são realizadas de forma análoga.

## <a name="import"></a>Importar

### <a name="set-up"></a>Configurar
Esta secção assume que tem as seguintes entidades:

- Um centro de notificação provisionado.
- Um recipiente de blob Azure Storage.
- Referências ao [pacote NuGet](https://www.nuget.org/packages/windowsazure.storage/) de Armazenamento Azure e pacote NuGet de Centros de [Notificação](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Crie o ficheiro de entrada e guarde-o numa bolha
Um ficheiro de entrada contém uma lista de registos serializados em XML, um por linha. Utilizando o Azure SDK, o seguinte exemplo de código mostra como serializar as matrículas e carregá-las para o recipiente blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> O código anterior serializa os registos na memória e, em seguida, envia todo o fluxo para uma bolha. Se já fez o upload de um ficheiro de mais do que alguns megabytes, consulte a orientação da bolha Azure sobre como executar estes passos; por exemplo, [blocos de bolhas.](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)

### <a name="create-url-tokens"></a>Criar fichas url
Uma vez que o seu ficheiro de entrada é carregado, gere os URLs para fornecer ao seu centro de notificação tanto para o ficheiro de entrada como para o diretório de saída. Pode utilizar dois recipientes de bolhas diferentes para entrada e saída.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Submeter o trabalho
Com os dois URLs de entrada e saída, agora pode iniciar o trabalho de lote.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Além dos URLs de entrada e saída, este exemplo cria um `NotificationHubJob` objeto que contém um `JobType` objeto, que pode ser um dos seguintes tipos:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Uma vez concluída a chamada, o trabalho é continuado pelo centro de notificação, e pode verificar o seu estado com a chamada para [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

No final do trabalho, pode inspecionar os resultados analisando os seguintes ficheiros no seu diretório de saída:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Estes ficheiros contêm a lista de operações bem sucedidas e falhadas do seu lote. O formato `.cvs`de ficheiro é, no qual cada linha tem o número de linha do ficheiro de entrada original, e a saída da operação (normalmente a descrição do registo criada ou atualizada).

### <a name="full-sample-code"></a>Código de amostra completo
O código de amostra seguinte importa registos num centro de notificação.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportar
O registo de exportação é semelhante ao importado, com as seguintes diferenças:

- Só precisa do URL de saída.
- Cria um NotificationHubJob de tipo ExportRegistrations.

### <a name="sample-code-snippet"></a>Corte de código de amostra
Aqui está um código de amostra para registos de exportação em Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as inscrições, consulte os seguintes artigos:

- [Gestão de registos](notification-hubs-push-notification-registration-management.md)
- [Etiquetas para registos](notification-hubs-tags-segment-push-message.md)
- [Registos de modelos](notification-hubs-templates-cross-platform-push-messages.md)
