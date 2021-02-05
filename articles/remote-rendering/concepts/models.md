---
title: Modelos
description: Descreve o que é um modelo na renderização remota do Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593879"
---
# <a name="models"></a>Modelos

Um *modelo* em Renderização Remota Azure refere-se a uma representação de objetos completos, composta por [entidades](entities.md) e [componentes.](components.md) Os modelos são a principal forma de obter dados personalizados no serviço de renderização remota.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente uma [entidade](entities.md) como nó raiz. Abaixo pode ter uma hierarquia arbitrária de entidades infantis. Ao carregar um modelo, é devolvida uma referência a esta entidade raiz.

Cada entidade pode ter [componentes ligados.](components.md) No caso mais comum, as entidades têm *MeshComponents,* que [referenciam recursos de malha.](meshes.md)

## <a name="creating-models"></a>Criação de modelos

A criação de modelos para tempo de execução é conseguida através da [conversão de modelos](../how-tos/conversion/model-conversion.md) de entrada de formatos de ficheiros como FBX e GLTF. O processo de conversão extrai todos os recursos, tais como texturas, materiais e malhas, e converte-os em formatos de tempo de execução otimizados. Extrairá igualmente a informação estrutural e convertê-la-á na estrutura de gráficos de entidade/componente da ARR.

> [!IMPORTANT]
> [A conversão do modelo](../how-tos/conversion/model-conversion.md) é a única forma de criar [malhas.](meshes.md) Embora as malhas possam ser partilhadas entre entidades em tempo de execução, não há outra forma de obter uma malha no tempo de execução, além de carregar um modelo.

## <a name="loading-models"></a>Modelos de carregamento

Uma vez convertido um modelo, pode ser carregado do armazenamento da bolha Azure para o tempo de execução.

Existem duas funções de carregamento distintas que diferem pela forma como o ativo é endereçado no armazenamento de bolhas:

* O modelo pode ser abordado diretamente por parâmetros de armazenamento de bolhas, caso o armazenamento do [blob esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante neste caso é `LoadModelAsync` com parâmetro `LoadModelOptions` .
* O modelo pode ser abordado pelo seu SAS URI. A função de carregamento relevante é `LoadModelFromSasAsync` com `LoadModelFromSasOptions` parâmetro. Utilize esta variante também ao carregar [modelos incorporados.](../samples/sample-model.md)

Os seguintes fragmentos de código mostram como carregar os modelos com qualquer uma das funções. Para carregar um modelo utilizando os seus parâmetros de armazenamento de bolhas, utilize código como o abaixo:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Se pretender carregar um modelo utilizando um token SAS, utilize um código semelhante ao seguinte corte:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Depois pode atravessar a hierarquia da entidade e modificar as entidades e componentes. Carregar o mesmo modelo várias vezes cria múltiplas instâncias, cada uma com a sua própria cópia da estrutura entidade/componente. Uma vez que as malhas, materiais e texturas são [recursos partilhados,](../concepts/lifetime.md)os seus dados não serão carregados novamente. Portanto, instantaneamente um modelo mais de uma vez incorre em relativamente pouca memória.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.LoadModelAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection.LoadModelFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection::LoadModelAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection::LoadModelFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Malhas](meshes.md)