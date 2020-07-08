---
title: Utilize o Blob Index para gerir e encontrar dados sobre o armazenamento do Azure Blob
description: Veja exemplos de como usar tags Blob Index para categorizar, gerir e consultar para descobrir objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.openlocfilehash: 6e3ce99211da35105fd9e118a850110dfd48ece1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84986277"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Utilize tags blob index (Preview) para gerir e encontrar dados sobre o armazenamento do Azure Blob

As tags Blob Index categorizam dados na sua conta de armazenamento utilizando atributos de etiqueta de valor chave. Estas tags são automaticamente indexadas e expostas como um índice multidimensional questionável para encontrar facilmente dados. Este artigo mostra-lhe como definir, obter e encontrar dados usando tags de índice de bolhas.

Para saber mais sobre o Índice Blob, consulte [Gerir e encontrar dados sobre o armazenamento de blob azure com índice blob (pré-visualização)](storage-manage-find-blobs.md).

> [!NOTE]
> O Blob Index está em pré-visualização pública, e está disponível nas regiões central do **Canadá,** **Canadá Oriental,** **França Central** e **França Sul.** Para saber mais sobre esta funcionalidade juntamente com questões e limitações conhecidas, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure blob com Índice blob (Preview)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Pré-requisitos
# <a name="portal"></a>[Portal](#tab/azure-portal)
- Subscrição registada e aprovada para acesso à pré-visualização do Blob Index
- Acesso ao [portal Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Uma vez que o Blob Index está em pré-visualização pública, o pacote de armazenamento .NET é lançado no feed NuGet de pré-visualização. Esta biblioteca está sujeita a mudanças entre agora e quando se tornar oficial. 

1. No Visual Studio, adicione o URL às fontes de `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` pacotes NuGet. 

   Para saber como, consulte [as fontes de pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)

2. No NuGet Package Manager, encontre o pacote **Azure.Storage.Blobs** e instale a versão **12.5.0-dev.20200422.2** para o seu projeto. Também pode executar o comando```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2```

   Para saber como, consulte [Localizar e instalar um pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)

3. Adicione as seguintes declarações usando a parte superior do seu ficheiro de código.
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

## <a name="upload-a-new-blob-with-index-tags"></a>Faça upload de uma nova bolha com etiquetas de índice
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** em **serviço Blob,** selecione o seu recipiente

3. Selecione o botão **Upload** para abrir a lâmina de upload e navegue no seu sistema de ficheiros local para encontrar um ficheiro para carregar como uma bolha de bloco.

4. Expanda a queda **avançada** e vá para a secção **Blob Index Tags**

5. Insira as tags de índice blob chave/valor que deseja aplicadas aos seus dados

6. Selecione o botão **Upload** para carregar a bolha

![Carregar dados com etiquetas de índice blob](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
O exemplo a seguir mostra como criar uma bolha de apêndice com etiquetas definidas durante a criação.
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

## <a name="get-set-and-update-blob-index-tags"></a>Obtenha, marque e atualize as etiquetas do índice blob
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** em **serviço Blob,** selecione o seu recipiente

3. Selecione a bolha desejada a partir da lista de bolhas dentro do recipiente selecionado

4. O separador de visão geral do blob mostrará as propriedades do seu blob, incluindo quaisquer **Tags de Índice Blob**

5. Pode obter, definir, modificar ou eliminar qualquer uma das tags de índice chave/valor para a sua bolha

6. Selecione o botão **Guardar** para confirmar quaisquer atualizações para a sua bolha

![Obtenha, marque, atualização e elimine as etiquetas de índice de blob em objetos](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

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

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrar e encontrar dados com etiquetas de índice blob

# <a name="portal"></a>[Portal](#tab/azure-portal)

Dentro do portal Azure, o filtro blob Index aplica automaticamente o parâmetro para o âmbito do `@container` seu recipiente selecionado. Se desejar filtrar e encontrar dados marcados em toda a sua conta de armazenamento, utilize as nossas API, SDKs ou ferramentas REST.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **Contentores** em **serviço Blob,** selecione o seu recipiente

3. Selecione o botão **de filtro blob Index para** filtrar dentro do recipiente selecionado

4. Introduza uma chave de etiqueta de índice Blob e valor de marcação

5. Selecione o botão **de filtro blob Index para** adicionar filtros de etiquetas adicionais (até 10)

![Filtrar e encontrar objetos marcados usando tags de índice de bolhas](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

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

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gestão do ciclo de vida com filtros de etiqueta de índice blob

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **de Gestão do Ciclo de Vida** no âmbito do Serviço **Blob**

3. Selecione *Adicionar regra* e, em seguida, preencha os campos de formulários definidos de Ação

4. Selecione o conjunto de filtro para adicionar filtro opcional para prefixo match e Blob Index match ![ Adicionar filtros de etiqueta de índice blob para gestão do ciclo de vida](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Selecione **Review + adicione** para rever as definições de regras Regra de ![ gestão do ciclo de vida com exemplo de filtro de etiquetas de índice blob](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. **Selecione Adicionar** para aplicar a nova regra à política de gestão do ciclo de vida

# <a name="net"></a>[.NET](#tab/net)
As políticas [de gestão do ciclo de vida](storage-lifecycle-management-concepts.md) são aplicadas para cada conta de armazenamento ao nível do plano de controlo. Para .NET, instale a [versão 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) ou superior da Microsoft Azure Management Storage Library para tirar partido do filtro de correspondência Blob Index dentro de uma regra de gestão do ciclo de vida.

---

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o Blob Index. Consulte [Gerir e encontrar dados sobre o armazenamento de blob Azure com índice blob (pré-visualização)](storage-manage-find-blobs.md )

Saiba mais sobre a Gestão do Ciclo de Vida. Ver [Gerir o ciclo de vida de armazenamento Azure Blob](storage-lifecycle-management-concepts.md)
