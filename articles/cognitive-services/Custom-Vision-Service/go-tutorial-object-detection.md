---
title: 'Início rápido: Criar um projeto de deteção de objeto com o SDK de visão personalizada para Go'
titlesuffix: Azure Cognitive Services
description: Criar um projeto, adicionar etiquetas, carregar imagens, preparar seu projeto e detetar objetos usando o SDK do Go.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 2/25/2018
ms.author: daauld
ms.openlocfilehash: 93a6d923aff49811a4b5b0bc2236af8d0bd4c067
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884904"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-go-sdk"></a>Início rápido: Criar um projeto de deteção de objeto com o SDK de Go de visão personalizada

Este artigo fornece informações e código de exemplo para ajudá-lo a começar a utilizar o SDK de visão personalizada com o Go para criar um modelo de deteção de objeto. Depois de criado, pode adicionar regiões etiquetadas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem através de programação. Utilize este exemplo como modelo para criar a sua própria aplicação Go.

## <a name="prerequisites"></a>Pré-requisitos

- [Go 1.8 +](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o serviço de visão personalizada SDK para Go, execute o seguinte comando no PowerShell:

```
go get -u github.com/Azure/azure-sdk-for-go/...
```

ou, se utiliza o dep, dentro do seu repositório, execute:
```
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo ficheiro chamado *Sample* no seu diretório de projeto preferencial.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas. Tenha em atenção que a diferença entre criar um projeto de classificação de imagens e um projeto de deteção de objetos é o domínio especificado na chamada **create_project**.

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.2/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample OD Project"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    var objectDetectDomain training.Domain
    domains, _ := trainer.GetDomains(ctx)

    for _, domain := range *domains.Value {
        fmt.Println(domain, domain.Type)
        if domain.Type == "ObjectDetection" && *domain.Name == "General" {
            objectDetectDomain = domain
            break
        }
    }
    fmt.Println("Creating project...")
    project, _ := trainer.CreateProject(ctx, project_name, "", objectDetectDomain.ID, "")
```

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar etiquetas de classificação ao seu projeto, adicione o seguinte código ao final da *Sample*:

```Go
    # Make two tags in the new project
    forkTag, _ := trainer.CreateTag(ctx, *project.ID, "fork", "A fork", string(training.Regular))
    scissorsTag, _ := trainer.CreateTag(ctx, *project.ID, "scissors", "Pair of scissors", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Ao etiquetar imagens em projetos de deteção de objeto, tem de especificar a região de cada objeto etiquetado com coordenadas normalizadas.

Para adicionar as imagens, etiquetas e regiões ao projeto, insira o seguinte código após a criação da etiqueta. Tenha em atenção que, para este tutorial, as regiões estão codificadas inline. As regiões especificam a caixa delimitadora em coordenadas normalizadas e as coordenadas são dadas pela ordem seguinte: esquerda, superior, largura, altura.

```Go
    forkImageRegions := map[string][4]float64{
        "fork_1.jpg": [4]float64{ 0.145833328, 0.3509314, 0.5894608, 0.238562092 },
        "fork_2.jpg": [4]float64{ 0.294117659, 0.216944471, 0.534313738, 0.5980392 },
        "fork_3.jpg": [4]float64{ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 },
        "fork_4.jpg": [4]float64{ 0.254901975, 0.185898721, 0.5232843, 0.594771266 },
        "fork_5.jpg": [4]float64{ 0.2365196, 0.128709182, 0.5845588, 0.71405226 },
        "fork_6.jpg": [4]float64{ 0.115196079, 0.133611143, 0.676470637, 0.6993464 },
        "fork_7.jpg": [4]float64{ 0.164215669, 0.31008172, 0.767156839, 0.410130739 },
        "fork_8.jpg": [4]float64{ 0.118872553, 0.318251669, 0.817401946, 0.225490168 },
        "fork_9.jpg": [4]float64{ 0.18259804, 0.2136765, 0.6335784, 0.643790841 },
        "fork_10.jpg": [4]float64{ 0.05269608, 0.282303959, 0.8088235, 0.452614367 },
        "fork_11.jpg": [4]float64{ 0.05759804, 0.0894935, 0.9007353, 0.3251634 },
        "fork_12.jpg": [4]float64{ 0.3345588, 0.07315363, 0.375, 0.9150327 },
        "fork_13.jpg": [4]float64{ 0.269607842, 0.194068655, 0.4093137, 0.6732026 },
        "fork_14.jpg": [4]float64{ 0.143382356, 0.218578458, 0.7977941, 0.295751631 },
        "fork_15.jpg": [4]float64{ 0.19240196, 0.0633497, 0.5710784, 0.8398692 },
        "fork_16.jpg": [4]float64{ 0.140931368, 0.480016381, 0.6838235, 0.240196079 },
        "fork_17.jpg": [4]float64{ 0.305147052, 0.2512582, 0.4791667, 0.5408496 },
        "fork_18.jpg": [4]float64{ 0.234068632, 0.445702642, 0.6127451, 0.344771236 },
        "fork_19.jpg": [4]float64{ 0.219362751, 0.141781077, 0.5919118, 0.6683006 },
        "fork_20.jpg": [4]float64{ 0.180147052, 0.239820287, 0.6887255, 0.235294119 },
    }
    
    scissorsImageRegions := map[string][4]float64{
        "scissors_1.jpg": [4]float64{ 0.4007353, 0.194068655, 0.259803921, 0.6617647 },
        "scissors_2.jpg": [4]float64{ 0.426470578, 0.185898721, 0.172794119, 0.5539216 },
        "scissors_3.jpg": [4]float64{ 0.289215684, 0.259428144, 0.403186262, 0.421568632 },
        "scissors_4.jpg": [4]float64{ 0.343137264, 0.105833367, 0.332107842, 0.8055556 },
        "scissors_5.jpg": [4]float64{ 0.3125, 0.09766343, 0.435049027, 0.71405226 },
        "scissors_6.jpg": [4]float64{ 0.379901975, 0.24308826, 0.32107842, 0.5718954 },
        "scissors_7.jpg": [4]float64{ 0.341911763, 0.20714055, 0.3137255, 0.6356209 },
        "scissors_8.jpg": [4]float64{ 0.231617644, 0.08459154, 0.504901946, 0.8480392 },
        "scissors_9.jpg": [4]float64{ 0.170343131, 0.332957536, 0.767156839, 0.403594762 },
        "scissors_10.jpg": [4]float64{ 0.204656869, 0.120539248, 0.5245098, 0.743464053 },
        "scissors_11.jpg": [4]float64{ 0.05514706, 0.159754932, 0.799019635, 0.730392158 },
        "scissors_12.jpg": [4]float64{ 0.265931368, 0.169558853, 0.5061275, 0.606209159 },
        "scissors_13.jpg": [4]float64{ 0.241421565, 0.184264734, 0.448529422, 0.6830065 },
        "scissors_14.jpg": [4]float64{ 0.05759804, 0.05027781, 0.75, 0.882352948 },
        "scissors_15.jpg": [4]float64{ 0.191176474, 0.169558853, 0.6936275, 0.6748366 },
        "scissors_16.jpg": [4]float64{ 0.1004902, 0.279036, 0.6911765, 0.477124184 },
        "scissors_17.jpg": [4]float64{ 0.2720588, 0.131977156, 0.4987745, 0.6911765 },
        "scissors_18.jpg": [4]float64{ 0.180147052, 0.112369314, 0.6262255, 0.6666667 },
        "scissors_19.jpg": [4]float64{ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 },
        "scissors_20.jpg": [4]float64{ 0.158088237, 0.04047389, 0.6691176, 0.843137264 },
    }
```
Em seguida, utilize este mapa de associações para carregar cada imagem de exemplo com as suas coordenadas de região. Adicione o seguinte código.

> [!NOTE]
> Terá de alterar o caminho para as imagens com base em onde transferiu o projeto de exemplos de SDK Go Cognitive Services anteriormente.

```Go
    // Go through the data table above and create the images
    fmt.Println("Adding images...")
    var fork_images []training.ImageFileCreateEntry
    for file, region := range forkImageRegions {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "fork", file))

        imageRegion := training.Region { 
            TagID:forkTag.ID,
            Left:&region[0],
            Top:&region[1],
            Width:&region[2],
            Height:&region[3],
        }

        fork_images = append(fork_images, training.ImageFileCreateEntry {
            Name: &file,
            Contents: &imageFile,
            Regions: &[]training.Region{ imageRegion },
        })
    }
        
    fork_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
        Images: &fork_images,
    })

    if (!*fork_batch.IsBatchSuccessful) {
        fmt.Println("Batch upload failed.")
    }

    var scissor_images []training.ImageFileCreateEntry
    for file, region := range scissorsImageRegions {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "scissors", file))

        imageRegion := training.Region { 
            TagID:scissorsTag.ID,
            Left:&region[0],
            Top:&region[1],
            Width:&region[2],
            Height:&region[3],
        }

        scissor_images = append(scissor_images, training.ImageFileCreateEntry {
            Name: &file,
            Contents: &imageFile,
            Regions: &[]training.Region{ imageRegion },
        })
    }
        
    scissor_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
        Images: &scissor_images,
     })
     
    if (!*scissor_batch.IsBatchSuccessful) {
        fmt.Println("Batch upload failed.")
    }    
```

### <a name="train-the-project"></a>Preparar o projeto

Este código cria a primeira iteração no projeto e marca-a como a iteração predefinida. A iteração predefinida reflete a versão do modelo que irá responder aos pedidos de predição. Deve atualizá-la sempre que voltar a preparar o modelo.

```go
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    fmt.Println("Training status:", *iteration.Status)
    for {
        if *iteration.Status != "Training" {
            break
        }
        time.Sleep(5 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
        fmt.Println("Training status:", *iteration.Status)
    }

    // Mark iteration as default
    *iteration.IsDefault = true
    trainer.UpdateIteration(ctx, *project.ID, *iteration.ID, iteration)
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Obter e utilizar o ponto final de predição predefinido

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_od_image.jpg"))
    results, _ := predictor.PredictImage(ctx, *project.ID, ioutil.NopCloser(bytes.NewReader(testImageData)), iteration.ID, "")

    for _, prediction := range *results.Predictions {
        boundingBox := *prediction.BoundingBox

        fmt.Printf("\t%s: %.2f%% (%.2f, %.2f, %.2f, %.2f)", 
            *prediction.TagName,
            *prediction.Probability * 100,
            *boundingBox.Left,
            *boundingBox.Top,
            *boundingBox.Width,
            *boundingBox.Height)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute *Sample*.

```PowerShell
go run sample.go
```

A saída da aplicação deverá aparecer na consola. Pode, em seguida, certificar-se de que a imagem de teste (encontrada em **amostras/visão/imagens/Teste**) está identificada de forma adequada e que a região de deteção está correta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora viu como cada passo do processo de deteção de objeto pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)