---
title: Visão geral do Runtime SDK
description: Familiarize-se com o SDK runtime de âncoras de objeto.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 551374824610c0257aaf52c45768d31849026524
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047546"
---
# <a name="runtime-sdk-overview"></a>Visão geral do Runtime SDK

Esta secção fornece uma visão geral de alto nível do SDK runtime de âncoras de objeto, que é usado para detetar objetos usando um modelo de Âncora de Objetos. Você vai entender como um objeto é representado e para que os vários componentes são usados.

Todos os tipos descritos abaixo podem ser encontrados no espaço de **nomes Microsoft.MixedReality.ObjectAnchors.**

## <a name="types"></a>Tipos

### <a name="objectmodel"></a>Modelo de Objeto

Um [ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) representa a geometria de um objeto físico e codifica os parâmetros necessários para a deteção e posição de estimativa. Deve ser criado utilizando o [serviço 'Âncoras de Objectos'.](../quickstarts/get-started-model-conversion.md) Em seguida, uma aplicação pode carregar o ficheiro de modelo gerado utilizando a API de Âncoras de Objetos e consultar a malha incorporada nesse modelo para visualização.

### <a name="objectsearcharea"></a>Área de ObjetoSearch

Um [ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) especifica o espaço para procurar um ou vários objetos. É definido por um nó de nó de gráfico espacial e limites espaciais no sistema de coordenadas representado pelo nó de gráfico espacial. O Object Anchors Runtime SDK suporta quatro tipos de limites, nomeadamente, **campo de visão,** **caixa de delimitação,** **esfera,** e uma **localização**.

### <a name="objectquery"></a>ObjetoQuery

Um [ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) diz a um **observador de objetos** como encontrar objetos de um dado modelo. Fornece os seguintes parâmetros incapazes, cujos valores padrão podem ser recuperados a partir de um modelo de objeto.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

A propriedade [MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) indica o valor a considerar uma instância como detetada.

Para cada candidato a objeto, um **observador** calcula a relação de superfícies sobrepostas entre o modelo de objeto transformado e a cena, então informa que o candidato a aplicação apenas quando o rácio de cobertura está acima de um determinado limiar.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

A propriedade [IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) indica se se espera que o objeto alvo fique no plano de terra.

Um plano térreo é o piso horizontal mais baixo da área de busca. Proporciona uma boa restrição nas possíveis poses de objetos. Ligar esta bandeira irá guiar o **observador** a estimar a pose num espaço limitado e poderá melhorar a precisão. Este parâmetro será ignorado se o modelo não for suposto estar no avião terrestre.

#### <a name="expectedmaxverticalorientationindegrees"></a>EsperadoMaxVerticalOrientationInDegrees

A propriedade [ExpectMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) indica o ângulo máximo esperado em graus entre a direção acima de uma instância de objeto e a gravidade.

Este parâmetro proporciona outro constrangimento na direção de cima de uma pose estimada. Por exemplo, se um objeto estiver à direita, este parâmetro pode ser 0. As âncoras de objetos não devem detetar objetos diferentes do modelo. Se um modelo estiver de cima para a direita, então não detetará um caso deitado de lado. Um novo modelo seria usado para layout lateral para baixo. A mesma regra aplica-se à articulação.

#### <a name="maxscalechange"></a>MaxScaleChange

A propriedade [MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) indica a alteração máxima da escala de objeto (dentro de 0 ~ 1) no que diz respeito ao mapeamento espacial. A escala estimada é aplicada a vértices de objetos transformados centrados na origem e alinhados com o eixo. As escalas estimadas podem não ser a escala real entre um modelo CAD e a sua representação física, mas alguns valores que permitem que a app torne um modelo de objeto próximo do mapeamento espacial no objeto físico.

#### <a name="searchareas"></a>Áreas de Busca

A propriedade [SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) indica uma variedade de limites espaciais onde encontrar objetos.

O **Observador** procurará objetos no espaço sindical de todas as áreas de busca especificadas numa consulta. Nesta libertação, voltaremos no máximo um objeto com maior confiança para reduzir a latência.

### <a name="objectinstance"></a>ObjetoInstância

Um [ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) representa uma posição hipotética em que um exemplo de um dado modelo pode estar no sistema de coordenadas HoloLens. Cada instância vem com uma `SurfaceCoverage` propriedade para indicar quão boa é a pose estimada.

Um caso é criado através do método de `ObjectObserver.DetectAsync` chamada, e depois atualizado automaticamente em segundo plano quando vivo. Uma aplicação pode ouvir o evento alterado pelo estado numa determinada instância ou alterar o modo de rastreio para parar/retomar a atualização. Uma instância será automaticamente removida do **observador** quando o rastreio for perdido.

### <a name="objectobserver"></a>ObjectObserver

Um [ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) carrega modelos de objetos, deteta as suas instâncias e reporta poses de 6-DoF de cada instância no sistema de coordenadas HoloLens.

Embora qualquer modelo ou instância de objeto seja criado a partir de um **observador,** as suas vidas são independentes. Uma aplicação pode eliminar um observador e continuar a utilizar o modelo ou instância do objeto.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

O [ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) regista diagnósticos e escreve dados para um arquivo.

Um arquivo de diagnóstico inclui a nuvem de ponto de cena, o estado do observador e informações sobre os modelos. Esta informação é útil para identificar possíveis problemas de tempo de execução. Para mais informações, consulte as [FAQ.](../faq.md)

## <a name="runtime-sdk-usage-and-details"></a>Utilização e detalhes do Runtime SDK

Esta secção deve fornecer-lhe o básico de como usar o Runtime SDK. Deve dar-lhe contexto suficiente para navegar através das aplicações da amostra para ver como as âncoras de objetos são usadas holisticamente.

### <a name="initialization"></a>Inicialização

As aplicações precisam de ligar para a `ObjectObserver.IsSupported()` API para determinar se as âncoras de objetos são suportadas no dispositivo antes de a utilizarem. Se a `ObjectObserver.IsSupported()` API `false` voltar, verifique se a aplicação permitiu a capacidade **de perceção espacial** e\ou atualização para o mais recente HoloLens OS.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Em seguida, a aplicação cria um observador de objetos e carrega os modelos necessários gerados pelo serviço de conversão do [modelo Object Anchors](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

A aplicação cria uma consulta para detetar casos desse modelo dentro de um espaço.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Por predefinição, cada instância detetada será rastreada automaticamente pelo **Observador**. Podemos manipular opcionalmente estes casos alterando o seu modo de rastreio ou ouvindo o seu evento alterado de estado.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

No evento alterado pelo estado, podemos consultar o estado mais recente ou descartar um caso se perdeu o rastreio.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Além disso, uma aplicação pode gravar opcionalmente uma ou múltiplas sessões de diagnóstico para depuragem offline.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Finalmente libertamos recursos eliminando todos os objetos.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```