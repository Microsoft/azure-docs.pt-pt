---
title: Utilize etiquetas de índice blob para gerir e encontrar dados no Azure Blob Storage
description: Veja exemplos de como usar tags de índice de bolhas para categorizar, gerir e consultar objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 159252cf850fd59f40d1b59e592153f50d7cb813
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371975"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Utilize tags de índice blob (pré-visualização) para gerir e encontrar dados sobre o Armazenamento Azure Blob

As etiquetas de índice blob categorizam dados na sua conta de armazenamento utilizando atributos de etiqueta de valor-chave. Estas tags são automaticamente indexadas e expostas como um índice multidimensional pesmável para encontrar facilmente dados. Este artigo mostra-lhe como definir, obter e encontrar dados usando tags de índice de bolhas.

> [!NOTE]
> O índice blob está em pré-visualização pública, e está disponível nas regiões central do **Canadá,** **Canadá Oriental,** **França Central** e **França Sul.** Para saber mais sobre esta funcionalidade juntamente com questões e limitações conhecidas, consulte [Gerir e encontrar dados do Azure Blob com etiquetas de índice blob (pré-visualização)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Uma assinatura Azure registada e aprovada para acesso à pré-visualização do índice blob
- Acesso ao [portal Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Uma vez que o índice blob está em pré-visualização, o pacote de armazenamento .NET é lançado no feed NuGet de pré-visualização. Esta biblioteca está sujeita a alterações durante o período de pré-visualização.

1. Crie o seu projeto Visual Studio para começar com a biblioteca de clientes Azure Blob Storage v12 para .NET. Para saber mais, consulte [.NET Quickstart](storage-quickstart-blobs-dotnet.md)

2. No Gestor de Pacotes NuGet, encontre o pacote **Azure.Storage.Blobs** e instale a versão **12.7.0-preview.1** ou mais recente no seu projeto. Também pode executar o comando PowerShell: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

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

O upload de uma nova bolha com etiquetas de índice pode ser realizado pelo Proprietário de [Dados blob de armazenamento](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Além disso, os utilizadores com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` permissão [de controlo de acesso baseada em funções](/azure/role-based-access-control/overview) podem realizar esta operação.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** sob **o serviço Blob,** selecione o seu recipiente

3. Selecione o botão **Upload** e navegue no seu sistema de ficheiros local para encontrar um ficheiro para carregar como uma bolha de bloco.

4. Expanda a queda **avançada** e vá para a secção **Blob Index Tags**

5. Insira as tags de índice blob chave/valor que deseja aplicadas aos seus dados

6. Selecione o botão **Upload** para carregar a bolha

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Screenshot do portal Azure mostrando como carregar uma bolha com etiquetas de índice.":::

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

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
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

Obter etiquetas de índice blob pode ser realizada pelo Proprietário de [Dados de Blob de Armazenamento](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Além disso, os utilizadores com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` permissão [de controlo de acesso baseada em funções](/azure/role-based-access-control/overview) podem realizar esta operação.

A definição e atualização das etiquetas do índice blob podem ser realizadas pelo Proprietário de [Dados de Blob de Armazenamento](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Além disso, os utilizadores com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` permissão [de controlo de acesso baseada em funções](/azure/role-based-access-control/overview) podem realizar esta operação.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento 

2. Navegue para a opção **Contentores** em **serviço Blob,** selecione o seu recipiente

3. Selecione a sua bolha a partir da lista de bolhas dentro do recipiente selecionado

4. O separador de visão geral do blob mostrará as propriedades do seu blob, incluindo quaisquer **Tags de Índice Blob**

5. Pode obter, definir, modificar ou eliminar qualquer uma das tags de índice chave/valor para a sua bolha

6. Selecione o botão **Guardar** para confirmar quaisquer atualizações para a sua bolha

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Screenshot do portal Azure mostrando como carregar uma bolha com etiquetas de índice.":::

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

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
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

A pesquisa e filtragem por etiquetas de índice blob podem ser realizadas pelo Proprietário de [Dados blob de armazenamento](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Além disso, os utilizadores com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` permissão [de controlo de acesso baseada em funções](/azure/role-based-access-control/overview) podem realizar esta operação.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Dentro do portal Azure, o filtro de etiquetas de índice blob aplica automaticamente o `@container` parâmetro para o âmbito do seu recipiente selecionado. Se desejar filtrar e encontrar dados marcados em toda a sua conta de armazenamento, utilize as nossas API REST, SDKs ou ferramentas.

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **Contentores** sob **o serviço Blob,** selecione o seu recipiente

3. Selecione o botão **de filtro blob Index para** filtrar dentro do recipiente selecionado

4. Introduza uma chave de etiqueta de índice blob e valor de etiqueta

5. Selecione o botão **de filtro blob Index para** adicionar filtros de etiquetas adicionais (até 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Screenshot do portal Azure mostrando como carregar uma bolha com etiquetas de índice.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
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
           
          // Blob index tags to upload
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

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
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

4. Selecione **conjunto de filtro** para adicionar filtro opcional para correspondência de prefixo e correspondência de índice de bolha

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Screenshot do portal Azure mostrando como carregar uma bolha com etiquetas de índice.":::

5. Selecione **Review + adicionar** para rever as definições de regras

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Screenshot do portal Azure mostrando como carregar uma bolha com etiquetas de índice.":::

6. **Selecione Adicionar** para aplicar a nova regra à política de gestão do ciclo de vida

# <a name="net"></a>[.NET](#tab/net)

As políticas [de gestão do ciclo de vida](storage-lifecycle-management-concepts.md) são aplicadas para cada conta de armazenamento ao nível do plano de controlo. Para .NET, instale a versão 16.0.0 ou superior da [Biblioteca de Armazenamento de Gestão do Microsoft Azure.](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/)

---

## <a name="next-steps"></a>Passos seguintes

 - Saiba mais sobre tags de índice blob, consulte [Gerir e encontrar dados do Azure Blob com etiquetas de índice blob (pré-visualização)](storage-manage-find-blobs.md )
 - Saiba mais sobre gestão do ciclo de vida, consulte [Gerir o ciclo de vida do Armazenamento Azure Blob](storage-lifecycle-management-concepts.md)
