---
title: Faça previsões com o Modelo AutoML ONNX em .NET
description: Saiba como fazer previsões utilizando um modelo AutoML ONNX em .NET com ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to
ms.openlocfilehash: cb4e57cfe8b7494b7d5c38869f83190bff76ef2a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305775"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Faça previsões com um modelo AutoML ONNX em .NET

Neste artigo, aprende-se a utilizar um modelo automatizado de ML (AutoML) Open Neural Network Exchange (ONNX) para fazer previsões numa aplicação de consola C# .NET Core com ML.NET.

[ML.NET](/dotnet/machine-learning/) é um quadro de aprendizagem automática de código aberto, transversal para o ecossistema .NET que permite treinar e consumir modelos de aprendizagem automática personalizados utilizando uma abordagem de código em C# ou F# bem como através de ferramentas de baixo código como [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) e o [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli). A estrutura também é extensível e permite-lhe alavancar outros quadros populares de aprendizagem automática como o TensorFlow e o ONNX.

O ONNX é um formato de código aberto para modelos de IA. A ONNX apoia a interoperabilidade entre quadros. Isto significa que você pode treinar um modelo em um dos muitos quadros populares de aprendizagem de máquinas como PyTorch, convertê-lo em formato ONNX, e consumir o modelo ONNX em um quadro diferente como ML.NET. Para saber mais, visite o site da [ONNX.](https://onnx.ai/)

## <a name="prerequisites"></a>Pré-requisitos

- [.NET Core SDK 3.1 ou maior](https://dotnet.microsoft.com/download)
- Editor de Texto ou IDE (como [Visual Studio](https://visualstudio.microsoft.com/vs/) ou Visual [Studio Code)](https://code.visualstudio.com/Download)
- Modelo ONNX. Para aprender a treinar um modelo AutoML ONNX, consulte o seguinte [caderno de classificação de marketing bancário.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)
- [Netron](https://github.com/lutzroeder/netron) (opcional)

## <a name="create-a-c-console-application"></a>Criar uma aplicação de consola C#

Nesta amostra, utiliza o CLI .NET Core para construir a sua aplicação, mas pode fazer as mesmas tarefas utilizando o Visual Studio. Saiba mais sobre o [.NET Core CLI](/dotnet/core/tools/).

1. Abra um terminal e crie uma nova aplicação de consola C# .NET Core. Neste exemplo, o nome da aplicação é `AutoMLONNXConsoleApp` . Um diretório é criado com o mesmo nome com o conteúdo da sua aplicação.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. No terminal, navegue para o *diretório AutoMLONNXConsoleApp.*

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Adicionar pacotes de software

1. Instale os pacotes **nuGet do Microsoft.ML** , **Microsoft.ML.OnnxRuntime** e **Microsoft.ML.OnnxTransformer** NuGet utilizando o .NET Core CLI.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Estas embalagens contêm as dependências necessárias para utilizar um modelo ONNX numa aplicação .NET. ML.NET fornece uma API que usa o [tempo de execução ONNX](https://github.com/Microsoft/onnxruntime) para previsões.

1. Abra o ficheiro *Program.cs* e adicione as `using` seguintes declarações no topo para fazer referência aos pacotes apropriados.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Adicione uma referência ao modelo ONNX

Uma forma de a aplicação da consola aceder ao modelo ONNX é adicioná-la ao diretório de saída de construção.  Para saber mais sobre os itens comuns da MSBuild, consulte o [guia MSBuild](/visualstudio/msbuild/common-msbuild-project-items).

Adicione uma referência ao seu ficheiro modelo ONNX na sua aplicação

1. Copie o seu modelo ONNX para o diretório de raiz *AutoMLONNXConsoleApp* da sua aplicação.
1. Abra o ficheiro *AutoMLONNXConsoleApp.csproj* e adicione o seguinte conteúdo dentro do `Project` nó.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    Neste caso, o nome do ficheiro modelo ONNX é *automl-model.onnx*.

1. Abra o ficheiro *Program.cs* e adicione a seguinte linha dentro da `Program` classe.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Inicializar O Texto MLContexto

Dentro do `Main` método da sua `Program` classe, crie um novo exemplo de [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

A [`MLContext`](xref:Microsoft.ML.MLContext) classe é um ponto de partida para todas as operações ML.NET, e a inicialização cria `mlContext` um novo ambiente ML.NET que pode ser partilhado através do ciclo de vida do modelo. É semelhante, conceptualmente, ao DbContext no Quadro de Entidades.

## <a name="define-the-model-data-schema"></a>Definir o esquema de dados do modelo

O seu modelo espera os seus dados de entrada e saída num formato específico. ML.NET permite definir o formato dos seus dados através das aulas. Às vezes já se sabe como é o formato. Nos casos em que não conhece o formato de dados, pode utilizar ferramentas como o Netron para inspecionar o seu modelo ONNX.

O modelo utilizado nesta amostra utiliza dados do conjunto de dados da NyC TLC Taxi Trip. Uma amostra dos dados pode ser vista abaixo:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>Inspecione o modelo ONNX (opcional)

Utilize uma ferramenta como a Netron para inspecionar as entradas e saídas do seu modelo.

1. Abre o Netron.
1. Na barra de menu superior, selecione **'Arquivo > Abra** e use o navegador de ficheiros para selecionar o seu modelo.
1. O teu modelo abre. Por exemplo, a estrutura do *modelo automl.onnx* parece o seguinte:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX Modelo":::

1. Selecione o último nó na parte inferior do gráfico `variable_out1` (neste caso) para visualizar os metadados do modelo. As entradas e saídas na barra lateral mostram-lhe as entradas, saídas e tipos de dados esperados do modelo. Utilize estas informações para definir o esquema de entrada e saída do seu modelo.

### <a name="define-model-input-schema"></a>Definir esquema de entrada de modelo

Crie uma nova classe chamada `OnnxInput` com as seguintes propriedades dentro do ficheiro *Program.cs.*

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Cada uma das propriedades mapeia para uma coluna no conjunto de dados. As propriedades são anotadas com atributos.

O [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atributo permite especificar como ML.NET deve referenciar a coluna ao operar os dados. Por exemplo, embora a `TripDistance` propriedade siga as convenções de nomeação padrão .NET, o modelo só sabe de uma coluna ou característica conhecida como `trip_distance` . Para resolver esta discrepância de nomeação, o [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atributo mapeia a `TripDistance` propriedade a uma coluna ou característica pelo nome `trip_distance` .
  
Para valores numéricos, ML.NET funciona apenas com [`Single`](xref:System.Single) tipos de valor. No entanto, o tipo de dados original de algumas das colunas são inteiros. Os [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) tipos de mapas de atributos entre ONNX e ML.NET.

Para saber mais sobre os atributos de dados, consulte o [guia de dados de ML.NET carregar](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definir esquema de saída de modelo

Uma vez processados os dados, produz uma saída de um determinado formato. Defina o seu esquema de saída de dados. Crie uma nova classe chamada `OnnxOutput` com as seguintes propriedades dentro do ficheiro *Program.cs.*

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Semelhante a `OnnxInput` , use o atributo para [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) mapear a `variable_out1` saída para um nome mais descritivo `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Definir um oleoduto de previsão

Um oleoduto em ML.NET é tipicamente uma série de transformações acorrentadas que operam nos dados de entrada para produzir uma saída. Para saber mais sobre as transformações de dados, consulte o [guia de transformação de dados ML.NET.](/dotnet/machine-learning/resources/transforms)

1. Criar um novo método chamado `GetPredictionPipeline` dentro da `Program` classe

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Defina o nome das colunas de entrada e saída. Adicione o seguinte código dentro do `GetPredictionPipeline` método.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Defina o seu oleoduto. Um [`IEstimator`](xref:Microsoft.ML.IEstimator%601) fornece uma planta das operações, entradas e esquemas de saída do seu oleoduto.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    Neste caso, [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) é a única transformação no pipeline, que requer os nomes das colunas de entrada e saída, bem como o caminho para o ficheiro do modelo ONNX.

1. Um [`IEstimator`](xref:Microsoft.ML.IEstimator%601) apenas define o conjunto de operações a aplicar aos seus dados. O que opera nos seus dados é conhecido como [`ITransformer`](xref:Microsoft.ML.ITransformer) . Utilize o `Fit` método para criar um a partir do seu `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    O [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) método espera uma entrada como entrada para realizar as [`IDataView`](xref:Microsoft.ML.IDataView) operações. Uma [`IDataView`](xref:Microsoft.ML.IDataView) é uma forma de representar dados em ML.NET usando um formato tabular. Dado que neste caso o gasoduto é utilizado apenas para previsões, pode fornecer um vazio [`IDataView`](xref:Microsoft.ML.IDataView) para fornecer [`ITransformer`](xref:Microsoft.ML.ITransformer) a informação necessária sobre o esquema de entrada e saída. O equipado [`ITransformer`](xref:Microsoft.ML.ITransformer) é então devolvido para posterior utilização na sua aplicação.

    > [!TIP]
    > Nesta amostra, o gasoduto é definido e utilizado dentro da mesma aplicação. No entanto, recomenda-se que utilize aplicações separadas para definir e utilizar o seu pipeline para fazer previsões. Na ML.NET os seus oleodutos podem ser serializados e guardados para posterior utilização noutras aplicações de utilizador final .NET. ML.NET suporta vários alvos de implementação, tais como aplicações de desktop, serviços web, aplicações WebAssembly*, e muito mais. Para saber mais sobre a poupança de gasodutos, consulte o [guia de modelos ML.NET economizadores e de carga.](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)
    >
    > *WebAssembly só é suportado em .NET Core 5 ou superior

1. Dentro do `Main` método, ligue para o `GetPredictionPipeline` método com os parâmetros necessários.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Use o modelo para fazer previsões

Agora que tens um oleoduto, está na altura de o usares para fazer previsões. ML.NET fornece uma API de conveniência para fazer previsões sobre uma única instância de dados chamada [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. Dentro do `Main` método, crie um [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) utilizando o [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) método.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Crie uma entrada de dados de teste.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Utilize o `predictionEngine` para fazer previsões com base nos novos `testInput` dados utilizando o [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) método.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Desemola o resultado da sua previsão para a consola.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Utilize o CLI .NET Core para executar a sua aplicação.

    ```dotnetcli
    dotnet run
    ```

    O resultado deve ser semelhante à seguinte saída:

    ```text
    Predicted Fare: 15.621523
    ```

Para saber mais sobre fazer previsões em ML.NET, consulte o [uso de um modelo para fazer guia de previsões.](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)

## <a name="next-steps"></a>Passos seguintes

- [Implemente o seu modelo como um ASP.NET Core Web API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Implemente o seu modelo como uma função de azure sem servidor .NET](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)