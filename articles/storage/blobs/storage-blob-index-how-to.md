---
title: Utilize o Blob Index para gerir e encontrar dados sobre o armazenamento do Blob Azure
description: Veja exemplos de como usar tags do Índice Blob para categorizar, gerir e consultar para descobrir objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 9ba151aa1ddc7f4b14d5f4ec7f1990e2fd760602
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121240"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Utilize tags do Índice Blob (Pré-visualização) para gerir e encontrar dados sobre o armazenamento do Blob Azure

O Índice Blob marca dados categoriza dados na sua conta de armazenamento utilizando atributos de etiqueta de valor-chave. Estas tags são automaticamente indexadas e expostas como um índice multidimensional consultavel para encontrar facilmente dados. Este artigo mostra-lhe como definir, obter e encontrar dados usando tags de índice blob.

Para saber mais sobre o Índice Blob, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob (pré-visualização)](storage-manage-find-blobs.md).

> [!NOTE]
> O Índice Blob está em pré-visualização pública, e está disponível nas regiões France **Central** e **France South.** Para saber mais sobre esta funcionalidade juntamente com questões e limitações conhecidas, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure com o Blob Index (Pré-visualização)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Pré-requisitos
# <a name="portal"></a>[Portal](#tab/azure-portal)
- Subscrição registada e aprovada para acesso à pré-visualização do Índice Blob
- Acesso ao [portal Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Como o Blob Index está em pré-visualização pública, o pacote de armazenamento .NET é lançado no feed NuGet de pré-visualização. Esta biblioteca está sujeita a mudanças entre agora e quando se torna oficial. 

1. No Estúdio Visual, adicione o URL às suas fontes de `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` pacote NuGet. 

   Para saber como, consulte [fontes de pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)

2. No NuGet Package Manager, encontre o pacote **Azure.Storage.Blobs** e instale a versão **12.5.0-dev.20200422.2** para o seu projeto. Também pode dirigir o comando```Install-Package Azure.Storage.Blobs -Version12.5.0-dev.20200422.2```

   Para saber como, consulte [Encontrar e instalar um pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)

3. Adicione as seguintes declarações utilizando a parte superior do seu ficheiro de código.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Faça upload de uma nova bolha com tags de índice
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** no âmbito do **Serviço Blob,** selecione o seu recipiente

3. Selecione o botão **Upload** para abrir a lâmina de upload e navegue no seu sistema de ficheiros local para encontrar um ficheiro para carregar como uma bolha de bloco.

4. Expandir a queda **avançada** e ir para a secção De Etiquetas de **Índice blob**

5. Insera as etiquetas de índice blob chave/valor que pretende aplicadas aos seus dados

6. Selecione o botão **Upload** para carregar a bolha

![Carregar dados com tags de índice blob](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
O exemplo que se segue mostra como criar uma bolha de apêndice com etiquetas definidas durante a criação.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Obter, definir e atualizar etiquetas de índice blob
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** no âmbito do **Serviço Blob,** selecione o seu recipiente

3. Selecione a sua bolha desejada a partir da lista de bolhas dentro do recipiente selecionado

4. O separador de visão geral blob mostrará as propriedades da sua bolha, incluindo quaisquer **etiquetas de índice blob**

5. Pode obter, definir, modificar ou eliminar qualquer uma das etiquetas de índice chave/valor para a sua bolha

6. Selecione o botão **Guardar** para confirmar quaisquer atualizações à sua bolha

![Obtenha, definir, atualizar e eliminar tags de índice blob em objetos](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrar e encontrar dados com tags de índice blob

# <a name="portal"></a>[Portal](#tab/azure-portal)

Dentro do portal Azure, o filtro de etiquetas do Índice Blob aplica automaticamente o `@container` parâmetro para examinar o seu recipiente selecionado. Se desejar filtrar e encontrar dados marcados em toda a sua conta de armazenamento, utilize os nossos API, SDKs ou ferramentas REST.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **Contentores** no âmbito do **Serviço Blob,** selecione o seu recipiente

3. Selecione o botão de **filtro de etiquetas do Índice Blob** para filtrar dentro do recipiente selecionado

4. Introduza uma chave de etiqueta do Índice Blob e o valor da etiqueta

5. Selecione o botão de **filtro de etiquetas do Índice Blob** para adicionar filtros de etiquetas adicionais (até 10)

![Filtrar e encontrar objetos marcados usando tags de índice blob](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gestão do ciclo de vida com filtros de etiquetas de índice blob

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **Gestão** de Ciclo de Vida no **âmbito do Serviço Blob**

3. Selecione *Adicionar regra* e, em seguida, preencher os campos de formulário de conjunto de ação

4. Selecione o conjunto de filtros para adicionar filtro opcional para a correspondência de prefixo e a correspondência do Índice blob Adicione filtros de ![ etiqueta seletiva para gestão do ciclo de vida](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Selecione **Review + adicione** para rever as definições de regra Regra de gestão do ciclo de vida com o exemplo do filtro de ![ etiquetas de índice blob](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Selecione **Adicionar** para aplicar a nova regra à política de gestão do ciclo de vida

# <a name="net"></a>[.NET](#tab/net)
As políticas de [gestão do ciclo](storage-lifecycle-management-concepts.md) de vida são aplicadas para cada conta de armazenamento ao nível do plano de controlo. Para .NET, instale a [versão 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) ou superior da Microsoft Azure Management Storage Library para tirar partido do filtro de correspondência do Blob Index dentro de uma regra de gestão do ciclo de vida.

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Índice Blob. Ver [Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob (pré-visualização)](storage-manage-find-blobs.md )

Saiba mais sobre gestão de ciclo de vida. Ver [Gerir o ciclo de vida de armazenamento de Blob Azure](storage-lifecycle-management-concepts.md)
