---
title: Modelos
description: Descreve o que é um modelo na Renderização Remota Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758695"
---
# <a name="models"></a>Modelos

Um *modelo* em Renderização Remota Azure refere-se a uma representação completa de [objetos,](entities.md) composto por entidades e [componentes.](components.md) Os modelos são a principal forma de obter dados personalizados no serviço de renderização remota.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente uma [entidade](entities.md) como o seu nó raiz. Abaixo pode ter uma hierarquia arbitrária de entidades infantis. Ao carregar um modelo, uma referência a esta entidade-raiz é devolvida.

Cada entidade pode ter [componentes](components.md) anexados. No caso mais comum, as entidades têm *Componentes de Malha,* que referenciam recursos de [malha.](meshes.md)

## <a name="creating-models"></a>Criação de modelos

A criação de modelos para o tempo de execução é conseguida através da conversão de [modelos](../how-tos/conversion/model-conversion.md) de entrada a partir de formatos de ficheiros como FBX e GLTF. O processo de conversão extrai todos os recursos, tais como texturas, materiais e malshes, e converte-os em formatos de tempo de execução otimizados. Extrairá igualmente a informação estrutural e converte-a na estrutura de gráficos de entidade/componente da ARR.

> [!IMPORTANT]
>
> [A conversão](../how-tos/conversion/model-conversion.md) do modelo é a única forma de criar [malshes.](meshes.md) Embora as malhas possam ser partilhadas entre entidades em tempo de execução, não há outra forma de colocar uma malha no tempo de corrido, além de carregar um modelo.

## <a name="loading-models"></a>Modelos de carregamento

Uma vez convertido um modelo, pode ser carregado do armazenamento de blob Azure para o tempo de funcionar.

Existem duas funções de carregamento distintas que diferem pela forma como o ativo é abordado no armazenamento de blob:

* O modelo pode ser abordado pelo seu SAS URI. A função de carga relevante é `LoadModelFromSASAsync` com parâmetro `LoadModelFromSASParams` . Utilize esta variante também ao carregar [modelos incorporados.](../samples/sample-model.md)
* O modelo pode ser abordado diretamente por parâmetros de armazenamento de bolhas, caso o [armazenamento de blob esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carga relevante neste caso é `LoadModelAsync` com parâmetro `LoadModelParams` .

Os seguintes fragmentos de código mostram como carregar modelos com qualquer uma das funções. Para carregar um modelo utilizando o SAS URI, utilize código como o abaixo:

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

Depois pode percorrer a hierarquia da entidade e modificar as entidades e componentes. Carregar o mesmo modelo várias vezes cria múltiplas instâncias, cada uma com a sua própria cópia da estrutura entidade/componente. Uma vez que as malshes, os materiais e as texturas são [recursos partilhados,](../concepts/lifetime.md)os seus dados não voltarão a ser carregados. Portanto, instantaneamente um modelo mais do que uma vez incorre relativamente pouco sobrecarga de memória.

> [!CAUTION]
> Todas as funções *De Sincronia* em ARR devolvem objetos de operação assíncronos. Deve armazenar uma referência a esses objetos até que a operação esteja concluída. Caso contrário, o coletor de lixo C# pode apagar a operação mais cedo e nunca poderá terminar. No código da amostra acima da utilização da *espera* garante que a variável local 'loadOp' detém uma referência até que o carregamento do modelo esteja terminado. No entanto, se utilizar o evento *Concluído,* terá de armazenar a operação assíncrona numa variável membro.

## <a name="next-steps"></a>Próximos passos

* [Entidades](entities.md)
* [Malhas](meshes.md)
