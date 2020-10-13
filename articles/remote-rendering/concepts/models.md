---
title: Modelos
description: Descreve o que é um modelo na renderização remota do Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e9c8c4a1209b8bb5be7af87ef22aeab0ffd90b79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90023776"
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

* O modelo pode ser abordado pelo seu SAS URI. A função de carregamento relevante é `LoadModelFromSASAsync` com `LoadModelFromSASParams` parâmetro. Utilize esta variante também ao carregar [modelos incorporados.](../samples/sample-model.md)
* O modelo pode ser abordado diretamente por parâmetros de armazenamento de bolhas, caso o armazenamento do [blob esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante neste caso é `LoadModelAsync` com parâmetro `LoadModelParams` .

Os seguintes fragmentos de código mostram como carregar os modelos com qualquer uma das funções. Para carregar um modelo utilizando o SAS URI, utilize código como o abaixo:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Se pretender carregar um modelo utilizando diretamente os seus parâmetros de armazenamento de bolhas, utilize código semelhante ao seguinte corte:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Depois pode atravessar a hierarquia da entidade e modificar as entidades e componentes. Carregar o mesmo modelo várias vezes cria múltiplas instâncias, cada uma com a sua própria cópia da estrutura entidade/componente. Uma vez que as malhas, materiais e texturas são [recursos partilhados,](../concepts/lifetime.md)os seus dados não serão carregados novamente. Portanto, instantaneamente um modelo mais de uma vez incorre em relativamente pouca memória.

> [!CAUTION]
> Todas as funções *Async* em ARR retornam objetos de operação assíncronos. Deve guardar uma referência a esses objetos até que a operação esteja concluída. Caso contrário, o coletor de lixo C# pode apagar a operação mais cedo e nunca poderá terminar. No código de amostra acima da utilização de *garantias* de que a variável local 'loadOp' mantém uma referência até que o carregamento do modelo esteja terminado. No entanto, se utilizar o evento *Complete,* terá de armazenar a operação assíncronea numa variável de membro.

## <a name="api-documentation"></a>Documentação da API

* [C# RemoteManager.LoadModelAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelasync)
* [C# RemoteManager.LoadModelFromSASAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelfromsasasync)
* [C++ RemoteManager::LoadModelAsync()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadmodelasync)
* [C++ RemoteManager::LoadModelFromSASAsync()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadmodelfromsasasync)

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Malhas](meshes.md)
