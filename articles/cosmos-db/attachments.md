---
title: Anexos DB Azure Cosmos
description: Este artigo apresenta uma visão geral dos anexos Azure Cosmos DB.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: a8e968d05a1f844a79d2e42d10c323ed4c392424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521225"
---
# <a name="azure-cosmos-db-attachments"></a>Anexos DB Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-mongodb-api.md)]

Os anexos DB da Azure Cosmos são itens especiais que contêm referências a metadados associados com uma bolha externa ou ficheiro de mídia.

A Azure Cosmos DB suporta dois tipos de anexos:

* **Os anexos não geridos** são um invólucro em torno de uma referência URI a uma bolha que é armazenada num serviço externo (por exemplo, Azure Storage, OneDrive, etc.). Esta abordagem é semelhante ao armazenamento de uma propriedade URI em um item padrão Azure Cosmos DB.
* **Os Anexos Geridos** são blobs geridos e armazenados internamente pela Azure Cosmos DB e expostos através de um mediaLink gerado pelo sistema.


> [!NOTE]
> O apego é uma característica antiga. O suporte do seu suporte encontra-se atento à funcionalidade continuada caso já esteja a utilizar esta funcionalidade.
> 
> Em vez de utilizar anexos, recomendamos que utilize o Azure Blob Storage como um serviço de armazenamento de bolhas construído de propósito para armazenar dados de bolhas . Pode continuar a armazenar metadados relacionados com bolhas, juntamente com ligações URI de referência, em Azure Cosmos DB como propriedades de item. Armazenar estes dados na Azure Cosmos DB fornece a capacidade de consultar metadados e ligações a blobs armazenados no Azure Blob Storage.
> 
> A Microsoft está empenhada em fornecer um aviso mínimo de 36 meses antes de depreciar totalmente os anexos – que serão anunciados numa nova data.

## <a name="known-limitations"></a>Limitações conhecidas

Os anexos geridos da Azure Cosmos DB são distintos do seu suporte para itens padrão – para os quais oferece escalabilidade ilimitada, distribuição global e integração com outros serviços Azure.

- Os anexos não são suportados em todas as versões dos SDKs da Azure Cosmos.
- Os anexos geridos estão limitados a 2 GB de armazenamento por conta de base de dados.
- Os anexos geridos não são compatíveis com a distribuição global da Azure Cosmos DB, e não são replicados em regiões.

> [!NOTE]
> A Azure Cosmos DB API para a versão 3.2 da MongoDB utiliza anexos geridos para GridFS e estão sujeitos às mesmas limitações.
>
> Recomendamos que os desenvolvedores utilizem o conjunto de funcionalidades MongoDB GridFS para atualizar para Azure Cosmos DB API para a versão 3.6 ou superior do MongoDB, que é dissociada dos anexos e proporciona uma melhor experiência. Em alternativa, os desenvolvedores que usam o conjunto de funcionalidades MongoDB GridFS também devem considerar a utilização do Azure Blob Storage - que é construído de propósito para armazenar conteúdo blob e oferece uma funcionalidade expandida a um custo mais baixo em comparação com o GridFS.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Anexos migratórios para armazenamento de blob Azure

Recomendamos a migração dos anexos DB da Azure Cosmos para o Armazenamento Azure Blob seguindo estes passos:

1. Copie os dados do seu recipiente DB Azure Cosmos para o seu recipiente de armazenamento Azure Blob.
2. Valide os dados de bolhas carregados no recipiente de armazenamento target Azure Blob.
3. Se aplicável, adicione referências URI às bolhas contidas no Azure Blob Storage como propriedades de cordas dentro do conjunto de dados Azure Cosmos DB.
4. Refafe o seu código de aplicação para ler e escrever bolhas do novo recipiente de armazenamento Azure Blob.

A seguinte amostra de código dotnet mostra como copiar anexos de Azure Cosmos DB para Azure Blob armazenamento como parte de um fluxo de migração usando Azure Cosmos DB's .NET SDK v2 e Azure Blob Storage .NET SDK v12. Certifique-se de que substitui `<placeholder values>` a conta DB da fonte Azure Cosmos e visa o recipiente de armazenamento Azure Blob.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Passos seguintes

- Começa com [o armazenamento da Azure Blob](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Obtenha referências para a utilização de anexos através [do .NET SDK v2 da Azure Cosmos DB](/dotnet/api/microsoft.azure.documents.attachment)
- Obtenha referências para a utilização de anexos através [de Java SDK v2 da Azure Cosmos DB](/java/api/com.microsoft.azure.documentdb.attachment)
- Obtenha referências para a utilização de anexos através [da API REST da Azure Cosmos DB](/rest/api/cosmos-db/attachments)
